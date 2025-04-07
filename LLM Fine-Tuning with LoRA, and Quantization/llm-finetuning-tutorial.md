# Fine-tuning Large Language Models: Theory and Implementation

This tutorial provides both theoretical understanding and practical implementation of fine-tuning Large Language Models (LLMs). We'll cover the key concepts behind fine-tuning, explain parameter-efficient methods like LoRA, understand quantization techniques, and then implement a complete fine-tuning workflow using a 3B parameter Llama model.

## Table of Contents
1. [Introduction to Fine-tuning LLMs](#introduction-to-fine-tuning-llms)
2. [Parameter-Efficient Fine-tuning](#parameter-efficient-fine-tuning)
3. [Quantization Techniques](#quantization-techniques)
4. [Instruction Fine-tuning](#instruction-fine-tuning)
5. [Dataset Preparation](#dataset-preparation)
6. [Implementation: Fine-tuning a 3B Llama Model](#implementation-fine-tuning-a-3b-llama-model)
7. [Evaluation and Model Performance](#evaluation-and-model-performance)
8. [Experiment Tracking with Weights & Biases](#experiment-tracking-with-weights--biases)

## Introduction to Fine-tuning LLMs

Large Language Models (LLMs) like GPT, Llama, and others are initially trained on vast corpora of text in a process called pre-training. During pre-training, the model learns general language patterns, grammar, factual knowledge, and basic reasoning capabilities. However, these general-purpose models may not excel at specific tasks or domains without additional training.

### What is Fine-tuning?

Fine-tuning is the process of further training a pre-trained model on a smaller, task-specific dataset to adapt it for particular applications. This process allows us to:

1. **Specialize a general model** for specific domains (medical, legal, financial)
2. **Align model responses** with desired formats or styles
3. **Improve performance** on specific tasks
4. **Reduce harmful outputs** by training on safety-aligned data

### Fine-tuning vs. Pre-training

| Aspect | Pre-training | Fine-tuning |
|--------|-------------|-------------|
| Dataset Size | Massive (billions of tokens) | Smaller (thousands to millions of tokens) |
| Compute Required | Enormous (hundreds/thousands of GPUs) | Moderate (can be done on a single GPU) |
| Learning Objective | General language modeling | Task-specific performance |
| Training Duration | Weeks to months | Hours to days |
| Parameters Modified | All parameters from scratch | All or subset of parameters |

However, fine-tuning large models presents significant challenges:

- **Memory constraints**: Full fine-tuning requires loading the entire model and gradients
- **Catastrophic forgetting**: Risk of losing general capabilities when training on narrow datasets
- **Compute requirements**: Even fine-tuning can be expensive for very large models

These challenges have led to the development of parameter-efficient fine-tuning methods.

## Parameter-Efficient Fine-tuning

Parameter-efficient fine-tuning (PEFT) techniques allow us to adapt large models by training only a small subset of parameters, dramatically reducing computational and memory requirements.

### Low-Rank Adaptation (LoRA)

LoRA is one of the most popular PEFT methods. It works by:

1. **Freezing the original model weights**
2. **Injecting trainable low-rank matrices** into specific layers of the model
3. **Significantly reducing the number of trainable parameters**

#### How LoRA Works

In a neural network, many weight matrices are over-parameterized and have a low "intrinsic rank." LoRA exploits this by approximating weight updates using low-rank decomposition:

Instead of updating a weight matrix W directly, LoRA decomposes the update into two smaller matrices:

```
ΔW = A × B
```

Where:
- A is a matrix of shape (d × r)
- B is a matrix of shape (r × k)
- r is the "rank" of the decomposition (much smaller than d and k)

This reduces trainable parameters from d×k to only r×(d+k).

![LoRA Illustration](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*AUDer-MFYnuzohWV91wDow.png)

During inference, the LoRA weights can be merged with the original weights for efficient deployment:

```
W' = W + ΔW = W + A × B
```

### Key LoRA Hyperparameters

1. **Rank (r)**: Controls the expressivity of the adaptation. Higher ranks provide more capacity but require more memory and computation.
   
2. **Alpha (α)**: A scaling factor that controls the magnitude of the LoRA update.
   
3. **Target Modules**: Which layers to apply LoRA to (often attention layers).

4. **Dropout**: Applied to LoRA matrices for regularization.

A key advantage of LoRA is that multiple adaptations can be easily swapped without reloading the full model.

## Quantization Techniques

Quantization reduces the precision of model weights to decrease memory usage and increase inference speed, with minimal impact on model quality.

### Types of Quantization

1. **Post-Training Quantization (PTQ)**: Applied after training is complete
   - Integer Quantization (INT8, INT4)
   - Half-precision (FP16)
   - Quarter-precision (NF4, FP4)

2. **Quantization-Aware Training (QAT)**: Model is trained with simulated quantization

### Bits and Bytes (BNB) Quantization

The `bitsandbytes` library provides efficient 4-bit quantization techniques:

1. **NF4 Format**: A 4-bit data type optimized for LLM weight distributions
2. **Double Quantization**: Further compresses the quantization constants
3. **Mixed Precision**: Uses different precision for different operations

Benefits of quantization:
- Reduced memory footprint (up to 4x smaller)
- Faster inference
- Enables larger models on limited hardware

## Instruction Fine-tuning

Instruction fine-tuning is a specialized form of fine-tuning that trains models to follow natural language instructions.

### Why Instruction Fine-tuning?

- Improves the model's ability to understand and follow directions
- Aligns model responses with human expectations
- Enhances usability for direct task specifications
- Better zero-shot and few-shot performance on new tasks

### Instruction Format

Instruction tuning typically uses a structured prompt format:

```
### Instruction:
[The task description]

### Input:
[Optional additional context]

### Response:
[Expected model output]
```

### Popular Instruction Datasets

1. **Alpaca**: Created by distilling outputs from GPT-4 based on instruction templates
2. **Self-Instruct**: Generated by having models create their own instruction examples
3. **FLAN**: A collection of tasks formatted as instructions
4. **Dolly**: Human-generated instruction-following examples

## Dataset Preparation

Proper dataset preparation is crucial for effective fine-tuning.

### Key Considerations

1. **Quality over Quantity**: High-quality examples are more important than large volumes
2. **Diversity**: Cover a range of desired capabilities and topics
3. **Format Consistency**: Maintain consistent instruction formats
4. **Task Balance**: Ensure important tasks are well-represented
5. **Data Cleaning**: Remove problematic or low-quality examples

### Formatting Functions

When preparing data, we typically create formatting functions that:
1. Convert raw examples into instruction format
2. Handle cases with and without additional input context
3. Apply consistent templating

## Implementation: Fine-tuning a 3B Llama Model

Now that we understand the theory, let's walk through the implementation steps.

### Setup and Dependencies

```python
# Install required packages
!python -m pip install -U wandb transformers trl datasets protobuf peft bitsandbytes accelerate sentencepiece

# Import key libraries
import wandb
import torch
from datasets import load_dataset
from transformers import AutoModelForCausalLM, AutoTokenizer, TrainingArguments, BitsAndBytesConfig
from peft import LoraConfig, get_peft_model
from trl import SFTTrainer
```

### Model Selection

For this tutorial, we're using `openlm-research/open_llama_3b_v2`, a 3B parameter open-source LLM based on the Llama architecture. This model is:
- Large enough to demonstrate fine-tuning principles
- Small enough to run on a single consumer GPU
- Based on the transformer architecture
- Accessible through the Hugging Face Hub

### LoRA Configuration

We configure LoRA with these parameters:

```python
peft_config = LoraConfig(
    r=64,               # Rank of the low-rank matrices
    lora_alpha=16,      # Scaling factor
    lora_dropout=0.1,   # Dropout for regularization
    bias="none",        # Don't train bias terms
    task_type="CAUSAL_LM",
    target_modules=["q_proj", "k_proj","v_proj","o_proj"], # Apply to attention layers
)
```

- **r=64**: Using a relatively high rank for better adaptation capacity
- **alpha=16**: Scaled to balance with the rank
- **target_modules**: Targeting all projection matrices in the attention mechanism

### Quantization Setup

We configure 4-bit quantization to reduce memory usage:

```python
bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_use_double_quant=True,
    bnb_4bit_compute_dtype=torch.float16
)
```

- **load_in_4bit**: Use 4-bit precision for weights
- **nf4**: Use the normalized float 4 format optimized for LLMs
- **double_quant**: Enable double quantization for constants
- **compute_dtype**: Perform calculations in half precision

### Training Configuration

The training parameters control the learning process:

```python
training_args = TrainingArguments(
    num_train_epochs=1,
    output_dir="./output/",
    per_device_train_batch_size=2,
    per_device_eval_batch_size=2,
    fp16=True,                   # Use mixed precision training
    learning_rate=2e-4,
    lr_scheduler_type="cosine",  # Learning rate decay schedule
    warmup_ratio=0.1,            # Gradual learning rate warmup
    gradient_accumulation_steps=16,
    gradient_checkpointing=True, # Save memory by recomputing activations
    evaluation_strategy="epoch",
    logging_strategy="steps",
    logging_steps=1,
    save_strategy="epoch",
    report_to="wandb",           # Track experiments with W&B
)
```

Key configuration choices:
- **Small batch size** (2) combined with **gradient accumulation** (16) to simulate larger batches
- **Gradient checkpointing** to reduce memory usage
- **Learning rate** set relatively high (2e-4) for LoRA training
- **Cosine schedule** with **warmup** for stable training

### Trainer Setup

We use the `SFTTrainer` from the TRL library, which is specialized for instruction fine-tuning:

```python
trainer = SFTTrainer(
    model=model_id,
    model_init_kwargs=model_kwargs,
    train_dataset=train_dataset,
    eval_dataset=eval_dataset,
    packing=True,              # Efficiently pack sequences
    max_seq_length=1024,       # Maximum sequence length
    args=training_args,
    formatting_func=create_prompt,  # Function to format examples
    peft_config=peft_config,   # LoRA configuration
)
```

The `SFTTrainer` handles:
- Loading and quantizing the base model
- Applying LoRA adapters
- Tokenizing and formatting the training data
- Implementing efficient training techniques

## Running the Training Process

With everything configured, training is initiated with:

```python
trainer.train()
```

This process:
1. Prepares batches from the training data
2. Performs forward and backward passes
3. Updates LoRA parameters
4. Evaluates on the validation set
5. Logs metrics and samples to W&B

## Evaluation and Model Performance

### Evaluation Metrics

Common evaluation metrics include:

1. **Loss**: Measures how well the model predicts tokens
2. **Generation Quality**: Human or automated evaluation of outputs
3. **Task-specific Metrics**: Accuracy, F1, BLEU, etc. for specific applications
4. **Inference Speed**: Impact on generation speed

### Sample-based Evaluation

In addition to metrics, evaluating generated samples gives insight into model behavior:

```python
wandb_callback = LLMSampleCB(trainer, test_dataset, num_samples=10, max_new_tokens=256)
trainer.add_callback(wandb_callback)
```

This callback:
- Takes incomplete prompts from the test set
- Generates completions with the current model
- Logs these samples to W&B for qualitative evaluation

## Experiment Tracking with Weights & Biases

Tracking experiments helps manage the fine-tuning process:

```python
wandb.init(project="alpaca_ft", 
           job_type="train",
           tags=["hf_sft_lora", "3b"])
```

W&B tracking provides:
1. **Metrics visualization**: Loss curves, learning rates
2. **Sample logging**: Generated text examples over time
3. **Hyperparameter tracking**: LoRA settings, training configurations
4. **Resource monitoring**: GPU utilization, memory usage
5. **Experiment comparison**: Compare different runs

## Complete Implementation

The complete implementation involves:

```python
# 1. Load and prepare the dataset
alpaca_ds = load_dataset("json", data_dir=artifact_dir)

# 2. Define formatting functions
def create_prompt(row):
    # Format examples into instruction format
    return prompt_no_input(row) if row["input"] == "" else prompt_input(row)

# 3. Configure LoRA and quantization
peft_config = LoraConfig(...)
bnb_config = BitsAndBytesConfig(...)

# 4. Set up training arguments
training_args = TrainingArguments(...)

# 5. Initialize the trainer
trainer = SFTTrainer(...)

# 6. Add evaluation callbacks
wandb_callback = LLMSampleCB(...)
trainer.add_callback(wandb_callback)

# 7. Run training
trainer.train()

# 8. Save and export the model
# The trainer automatically saves checkpoints based on save_strategy
```

## Next Steps and Best Practices

1. **Iterative Improvement**:
   - Start with a small dataset, then scale up
   - Examine samples and refine your approach

2. **Hyperparameter Optimization**:
   - Experiment with different LoRA ranks
   - Try different learning rates and schedules
   - Adjust target modules (which layers to apply LoRA to)

3. **Data Quality**:
   - Focus on improving data quality over quantity
   - Ensure examples cover your target applications
   - Consider data augmentation techniques

4. **Evaluation**:
   - Develop robust evaluation protocols
   - Test on out-of-distribution examples
   - Compare against baseline models

5. **Deployment Considerations**:
   - Merge LoRA weights for efficient serving
   - Benchmark inference performance
   - Consider distillation for further optimization

## Conclusion

Fine-tuning LLMs with parameter-efficient methods like LoRA offers a powerful way to adapt large models to specific tasks without requiring massive computational resources. By understanding both the theoretical foundations and practical implementation details, you can effectively customize models for your specific applications.

This tutorial has covered the key concepts, techniques, and implementation details for fine-tuning LLMs. By combining the theoretical understanding with hands-on practice, you're now equipped to experiment with your own fine-tuning projects.
