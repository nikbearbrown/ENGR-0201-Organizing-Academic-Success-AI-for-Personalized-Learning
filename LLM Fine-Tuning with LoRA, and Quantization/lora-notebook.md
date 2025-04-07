# Low-Rank Adaptation (LoRA): Theory and Implementation

## Table of Contents
1. [Introduction](#introduction)
2. [Foundations: Working with Large Models](#foundations-working-with-large-models)
   - [Precision and Quantization](#precision-and-quantization)
   - [Floating Point Representation](#floating-point-representation)
   - [Model Size Calculations](#model-size-calculations)
   - [Mixed Precision and 16-bit Networks](#mixed-precision-and-16-bit-networks)
3. [Quantization](#quantization)
   - [Optimal Bit Settings](#optimal-bit-settings)
   - [FLOPS and Hardware Performance](#flops-and-hardware-performance)
4. [Parameter-Efficient Fine-Tuning](#parameter-efficient-fine-tuning)
   - [Traditional Transfer Learning](#traditional-transfer-learning)
   - [Adapter Layers](#adapter-layers)
   - [Prefix Tuning](#prefix-tuning)
5. [Low-Rank Adaptation (LoRA)](#low-rank-adaptation-lora)
   - [Understanding Matrix Rank](#understanding-matrix-rank)
   - [Intrinsic Dimensionality](#intrinsic-dimensionality)
   - [Rank Decomposition in LoRA](#rank-decomposition-in-lora)
   - [LoRA Forward Pass](#lora-forward-pass)
   - [Scaling Factor Alpha](#scaling-factor-alpha)
   - [Optimal Rank Selection](#optimal-rank-selection)
   - [Benefits of LoRA](#benefits-of-lora)
6. [QLoRA: Quantized Low-Rank Adaptation](#qlora-quantized-low-rank-adaptation)
7. [Implementing LoRA with Python](#implementing-lora-with-python)
   - [Basic Implementation](#basic-implementation)
   - [Using Hugging Face's PEFT Library](#using-hugging-faces-peft-library)
   - [Complete Example: Fine-tuning GPT-2 with LoRA](#complete-example-fine-tuning-gpt-2-with-lora)

## Introduction

As deep learning models continue to grow in size, especially in the realm of language modeling, fine-tuning these models efficiently has become a significant challenge. Modern language models like GPT-4 have reached approximately 1.8 trillion parameters, requiring substantial computational resources for training and fine-tuning.

This notebook focuses on Low-Rank Adaptation (LoRA), a parameter-efficient fine-tuning technique that allows adaptation of large models with significantly fewer trainable parameters. We'll explore both the theoretical foundations and practical implementation details.

## Foundations: Working with Large Models

### Precision and Quantization

Neural network weights are typically stored as floating-point numbers. Understanding how these values are represented and stored is crucial for optimizing large models.

### Floating Point Representation

A standard 32-bit floating-point number (float32) uses:
- 1 bit for the sign
- 8 bits for the exponent
- 23 bits for the fraction (mantissa)

For example, representing 7.5 in float32 format:
```
Sign | Exponent | Fraction
  0  | 10000001 | 11100000000000000000000
```

### Model Size Calculations

The memory footprint of a model can be calculated as:
```
Model Size (bytes) = Number of Parameters × Size of Data Type
```

For example, BLOOM (176 billion parameters) requires approximately:
- For inference (float32): 176B × 4 bytes ≈ 704GB
- Using half precision (float16): 176B × 2 bytes ≈ 352GB

For training, you need additional memory for gradients and optimizer states, roughly 3x the model size.

### Mixed Precision and 16-bit Networks

Using half precision (float16) provides:
- 50% memory reduction
- Approximately 2x speed improvement in FLOPS
- Some loss in numerical precision

Mixed precision training uses different precisions for different parts of the network to balance accuracy and performance.

## Quantization

Quantization goes beyond simply reducing precision by intelligently mapping floating-point values to lower-bit representations.

```python
import numpy as np

# Example of simple 8-bit quantization
def quantize_to_int8(float_tensor, scale_factor=None):
    """
    Quantize a float32 tensor to int8
    """
    if scale_factor is None:
        # Calculate scale factor based on tensor range
        abs_max = np.abs(float_tensor).max()
        scale_factor = 127.0 / abs_max
    
    # Quantize
    quantized = np.round(float_tensor * scale_factor).astype(np.int8)
    
    return quantized, scale_factor

# Example usage
original = np.array([[0.123, -0.456, 0.789], 
                     [-0.987, 0.654, -0.321]], dtype=np.float32)
                     
quantized, scale = quantize_to_int8(original)

# Dequantize
dequantized = quantized.astype(np.float32) / scale

print("Original:\n", original)
print("Quantized (int8):\n", quantized)
print("Dequantized:\n", dequantized)
print(f"Memory reduction: {original.nbytes / quantized.nbytes}x")
```

### Optimal Bit Settings

Research suggests that 4-bit quantization is often optimal for the trade-off between model performance and memory efficiency.

### FLOPS and Hardware Performance

FLOPS (Floating Point Operations Per Second) measures hardware computational performance. Modern GPUs provide substantially higher FLOPS when using lower precision:

| GPU Model | FP32 TFLOPS | FP16 TFLOPS | INT8 TOPS |
|-----------|-------------|-------------|-----------|
| A100      | 19.5        | 312         | 624       |
| RTX 4090  | 82.6        | 165         | 661       |

## Parameter-Efficient Fine-Tuning

Several approaches exist for fine-tuning large models efficiently:

### Traditional Transfer Learning

Classic approach: Freeze the pre-trained model and only train a new task-specific head.
- Pros: Very efficient, no changes to original model
- Cons: Limited to using only the final layer representations

### Adapter Layers

Insert small trainable modules between layers of the frozen pre-trained model.
- Pros: More expressive than traditional transfer learning
- Cons: Increases inference latency, adds architectural complexity

```python
import torch.nn as nn

class AdapterLayer(nn.Module):
    def __init__(self, input_dim, adapter_dim):
        super().__init__()
        self.down = nn.Linear(input_dim, adapter_dim)
        self.activation = nn.ReLU()
        self.up = nn.Linear(adapter_dim, input_dim)
        
    def forward(self, x):
        return x + self.up(self.activation(self.down(x)))
```

### Prefix Tuning

Prepend trainable vectors to the input sequence to influence the model's behavior.
- Pros: Very parameter-efficient, focused on input modification
- Cons: Limited expressivity, mainly for language models

```python
class PrefixTuning(nn.Module):
    def __init__(self, prefix_length, embedding_dim):
        super().__init__()
        self.prefix_embedding = nn.Parameter(
            torch.randn(prefix_length, embedding_dim)
        )
        
    def forward(self, embeddings):
        # Concatenate prefix embeddings with input embeddings
        return torch.cat([self.prefix_embedding, embeddings], dim=0)
```

## Low-Rank Adaptation (LoRA)

### Understanding Matrix Rank

The rank of a matrix represents the number of linearly independent rows or columns it contains. It measures the "information content" or "degrees of freedom" in the matrix.

For an m×n matrix, the maximum possible rank is min(m,n).

Example:
```python
import numpy as np

# Full rank matrix (rank = 3)
full_rank = np.array([
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9] + [0.1, 0.2, 0.3]  # Adding small values to make it full rank
])

# Low rank matrix (rank = 1)
low_rank = np.array([
    [1, 2, 3],
    [2, 4, 6],
    [3, 6, 9]
])

print(f"Full rank matrix rank: {np.linalg.matrix_rank(full_rank)}")
print(f"Low rank matrix rank: {np.linalg.matrix_rank(low_rank)}")
```

### Intrinsic Dimensionality

Research has shown that the change in weights during fine-tuning often has a low "intrinsic dimension" - meaning it can be effectively represented by a much smaller number of parameters.

For large pre-trained models, the larger the model, the lower the intrinsic dimension needed for adaptation to specific tasks.

### Rank Decomposition in LoRA

LoRA applies a low-rank decomposition to the weight updates:

$$W = W_0 + \Delta W = W_0 + BA$$

Where:
- $W_0$ is the original pre-trained weight matrix (frozen)
- $\Delta W$ is the update matrix
- $B$ and $A$ are low-rank matrices of shapes (d × r) and (r × k) where r ≪ min(d, k)

```python
import torch
import torch.nn as nn

class LoRALayer(nn.Module):
    def __init__(self, in_features, out_features, rank=4, alpha=1):
        super().__init__()
        self.original_layer = nn.Linear(in_features, out_features)
        # Freeze the original weights
        for param in self.original_layer.parameters():
            param.requires_grad = False
            
        # Low-rank matrices
        self.lora_A = nn.Parameter(torch.zeros(in_features, rank))
        self.lora_B = nn.Parameter(torch.zeros(rank, out_features))
        
        # Initialize A with random weights
        nn.init.kaiming_uniform_(self.lora_A, a=math.sqrt(5))
        
        # Scale factor
        self.alpha = alpha
        self.rank = rank
        
    def forward(self, x):
        # Original forward pass
        original_output = self.original_layer(x)
        
        # LoRA forward pass
        lora_output = (x @ self.lora_A) @ self.lora_B
        
        # Scale and add
        return original_output + (lora_output * (self.alpha / self.rank))
```

### LoRA Forward Pass

In the forward pass:
1. The input goes through both the frozen original weights and the LoRA path
2. The LoRA path computes: input → A → B
3. This result is scaled by (alpha / rank)
4. The scaled result is added to the original output

### Scaling Factor Alpha

The scaling factor alpha balances the contribution of the original model and the fine-tuned components:
- α = 0: Use only the original model weights
- α = 1: Standard LoRA adaptation
- α > 1: Emphasize the fine-tuned components

Dividing by the rank (r) helps normalize the contribution based on the decomposition size.

### Optimal Rank Selection

Choosing the right rank involves a trade-off:
- Lower rank: More parameter-efficient, faster training
- Higher rank: More expressive, potentially better performance

The optimal rank depends on:
- The similarity between pre-training and fine-tuning data
- The complexity of the adaptation task
- The size of the original model

Empirically, ranks between 4 and 64 work well for most applications.

### Benefits of LoRA

1. **Efficiency**: Drastically reduces trainable parameters (often <1% of the original model)
2. **No inference overhead**: LoRA weights can be merged with the original model
3. **Swappable adaptations**: Multiple fine-tuned versions can be stored and swapped without loading the full model

## QLoRA: Quantized Low-Rank Adaptation

QLoRA combines 4-bit quantization with LoRA to further reduce memory requirements:
- Original model weights are quantized to 4-bit
- LoRA parameters remain in higher precision
- Enables fine-tuning of very large models on consumer hardware

## Implementing LoRA with Python

### Basic Implementation

Here's a basic LoRA implementation for a linear layer:

```python
import torch
import torch.nn as nn
import math

class LoRALinear(nn.Module):
    def __init__(self, original_layer, rank=8, alpha=1):
        super().__init__()
        self.original_layer = original_layer
        
        # Freeze original weights
        self.original_layer.weight.requires_grad = False
        if self.original_layer.bias is not None:
            self.original_layer.bias.requires_grad = False
            
        # Get dimensions
        in_features = self.original_layer.in_features
        out_features = self.original_layer.out_features
        
        # Initialize LoRA matrices
        self.lora_A = nn.Parameter(torch.zeros(in_features, rank))
        self.lora_B = nn.Parameter(torch.zeros(rank, out_features))
        
        # Initialize A with random weights
        nn.init.kaiming_uniform_(self.lora_A, a=math.sqrt(5))
        
        # Scale factor
        self.scale = alpha / rank
        
    def forward(self, x):
        # Original forward pass
        original_output = self.original_layer(x)
        
        # LoRA contribution
        lora_output = (x @ self.lora_A) @ self.lora_B
        
        return original_output + (lora_output * self.scale)
```

### Using Hugging Face's PEFT Library

The Parameter-Efficient Fine-Tuning (PEFT) library from Hugging Face makes it easy to apply LoRA to transformer models:

```python
from transformers import AutoModelForCausalLM, AutoTokenizer
from peft import get_peft_model, LoraConfig, TaskType

# Load base model
model_name = "gpt2"
model = AutoModelForCausalLM.from_pretrained(model_name)
tokenizer = AutoTokenizer.from_pretrained(model_name)

# Define LoRA configuration
peft_config = LoraConfig(
    task_type=TaskType.CAUSAL_LM,
    r=8,                              # LoRA rank
    lora_alpha=16,                    # LoRA scaling factor
    lora_dropout=0.1,                 # Dropout probability for LoRA layers
    target_modules=["c_attn", "c_proj"],  # Which modules to apply LoRA to
)

# Apply LoRA to model
model = get_peft_model(model, peft_config)

# Print trainable parameters
trainable_params = sum(p.numel() for p in model.parameters() if p.requires_grad)
total_params = sum(p.numel() for p in model.parameters())
print(f"Trainable parameters: {trainable_params} ({trainable_params/total_params:.2%} of total)")
```

### Complete Example: Fine-tuning GPT-2 with LoRA

Here's a complete example of fine-tuning GPT-2 with LoRA on a custom dataset:

```python
import torch
from transformers import AutoModelForCausalLM, AutoTokenizer, Trainer, TrainingArguments
from peft import get_peft_model, LoraConfig, TaskType
from datasets import load_dataset

# 1. Load model
model_name = "gpt2"
model = AutoModelForCausalLM.from_pretrained(model_name)
tokenizer = AutoTokenizer.from_pretrained(model_name)
tokenizer.pad_token = tokenizer.eos_token

# 2. Configure LoRA
lora_config = LoraConfig(
    r=8,
    lora_alpha=32,
    lora_dropout=0.1,
    bias="none",
    task_type=TaskType.CAUSAL_LM,
    target_modules=["c_attn", "c_proj"]  # Attention matrices for GPT-2
)

# 3. Apply LoRA
model = get_peft_model(model, lora_config)
model.print_trainable_parameters()  # Should be around 0.1-0.5% of total parameters

# 4. Load and preprocess dataset
dataset = load_dataset("imdb")  # Example: IMDB movie reviews

def tokenize_function(examples):
    return tokenizer(
        examples["text"], 
        padding="max_length", 
        truncation=True, 
        max_length=512
    )

tokenized_datasets = dataset.map(tokenize_function, batched=True)
tokenized_datasets = tokenized_datasets.remove_columns(
    [col for col in dataset["train"].column_names if col != "label"]
)
tokenized_datasets = tokenized_datasets.rename_column("label", "labels")

# 5. Setup training arguments
training_args = TrainingArguments(
    output_dir="./results",
    num_train_epochs=3,
    per_device_train_batch_size=4,
    gradient_accumulation_steps=8,
    warmup_steps=100,
    weight_decay=0.01,
    logging_dir="./logs",
    logging_steps=10,
    save_strategy="epoch",
    fp16=True,  # Use mixed precision
)

# 6. Train model
trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=tokenized_datasets["train"],
    eval_dataset=tokenized_datasets["test"],
)

trainer.train()

# 7. Save fine-tuned model
model.save_pretrained("gpt2-lora-imdb")

# 8. To use the model, merge weights (optional)
merged_model = model.merge_and_unload()
merged_model.save_pretrained("gpt2-lora-imdb-merged")
```

## Conclusion

Low-Rank Adaptation (LoRA) offers a highly efficient approach to fine-tuning large models without the need for extensive computational resources. By focusing on weight updates through low-rank decomposition, LoRA significantly reduces the number of trainable parameters while maintaining performance comparable to full fine-tuning.

As models continue to grow in size, techniques like LoRA and QLoRA will become increasingly important for democratizing access to fine-tuning capabilities, enabling more researchers and developers to adapt large foundation models to specific tasks.
