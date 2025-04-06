# Fine-Tuning Large Language Models: A Practical Guide

This notebook demonstrates how to fine-tune a 3B parameter language model on instruction data using:

- **Weights & Biases (W&B)** for experiment tracking and visualization
- **LoRA (Low-Rank Adaptation)** for parameter-efficient fine-tuning
- **Quantization** for memory efficiency
- **HuggingFace Transformers** for the ML infrastructure

While this guide uses W&B for experiment tracking, other alternatives include MLflow, TensorBoard, and Neptune.ai.

## Prerequisites: W&B Account Setup

Before running this notebook, you'll need a Weights & Biases (W&B) account and API key:

1. **Create a W&B account** if you don't have one already:
   - Go to [wandb.ai/signup](https://wandb.ai/signup)
   - The free tier includes unlimited public projects and 100GB of artifact storage

2. **Get your API key**:
   - Log in to W&B
   - Click your profile icon in the top-right corner
   - Select "Settings"
   - Scroll to the "API keys" section
   - Copy your existing key or create a new one

3. **Use your API key**:
   - When prompted with "wandb: Paste an API key from your profile and hit enter:", paste your key
   - Alternatively, you can set it programmatically before running any W&B code:
   ```python
   import os
   os.environ["WANDB_API_KEY"] = "your-api-key-here"  # Replace with your actual key
   ```

## 1. Installation and Setup

First, we need to install all the necessary libraries to support our fine-tuning workflow:

```python
# Install required libraries
# wandb - for experiment tracking
# transformers - for working with transformer models
# trl - for supervised fine-tuning
# datasets - for data handling
# peft - for parameter-efficient fine-tuning (includes LoRA)
# bitsandbytes - for quantization
# accelerate - for distributed training
!python -m pip install -U wandb transformers trl datasets "protobuf==3.20.3" evaluate peft bitsandbytes accelerate sentencepiece -qqq

# Download the utils file which contains helper functions
!wget https://github.com/wandb/edu/raw/main/llm-training-course/colab/utils.py
```

The `utils.py` file contains several helper functions that we'll use in this notebook:

- **LLMSampleCB**: A custom W&B callback that logs model generations during training
- **load_model_from_artifact and save_model**: Functions to load and save models to W&B
- **param_count**: Utility to count parameters in a model
- **freeze**: Function for freezing model layers

## 2. Dataset Preparation with W&B

W&B Artifacts provide a way to version and track datasets, models, and other assets. Here we use an Artifact to retrieve our instruction dataset:

```python
import wandb

# Initialize a W&B run for tracking this experiment
wandb.init(
    project="alpaca_ft",  # The project name in W&B
    job_type="train",     # The type of job
    tags=["hf_sft_lora", "3b"]  # Tags for filtering experiments
)

# Download the Alpaca dataset using W&B Artifacts
# This dataset contains instruction-response pairs curated with GPT-4
artifact = wandb.use_artifact('capecape/alpaca_ft/alpaca_gpt4_splitted:v4', type='dataset')
artifact_dir = artifact.download()

# Load the dataset using HuggingFace datasets
from datasets import load_dataset
alpaca_ds = load_dataset("json", data_dir=artifact_dir)

# For demonstration purposes, we'll use a small subset of the data
# In a real scenario, you'd use the full dataset
alpaca_ds['train'] = alpaca_ds['train'].select(range(512))  # 512 training examples
alpaca_ds['test'] = alpaca_ds['test'].select(range(10))     # 10 test examples
```

## 3. Preparing Instruction Format

When fine-tuning LLMs on instruction data, we need a consistent format that the model can learn to follow:

```python
# Function for formatting examples without additional input
def prompt_no_input(row):
    return ("Below is an instruction that describes a task. "
            "Write a response that appropriately completes the request.\n\n"
            "### Instruction:\n{instruction}\n\n### Response:\n{output}").format_map(row)

# Function for formatting examples with additional input
def prompt_input(row):
    return ("Below is an instruction that describes a task, paired with an input that provides further context. "
            "Write a response that appropriately completes the request.\n\n"
            "### Instruction:\n{instruction}\n\n### Input:\n{input}\n\n### Response:\n{output}").format_map(row)

# Choose the appropriate function based on whether input is provided
def create_prompt(row):
    return prompt_no_input(row) if row["input"] == "" else prompt_input(row)

# Prepare datasets for training and evaluation
train_dataset = alpaca_ds["train"]
eval_dataset = alpaca_ds["test"]
```

This formatting is crucial because:
1. The model learns to associate this specific structure with the instruction-following task
2. Clear markers like "### Instruction:" and "### Response:" help the model understand its role
3. During inference, we'll use the same format but omit the response section

## 4. Setting Up the Base Model

We'll use a relatively small but capable model as our starting point:

```python
import torch
from transformers import AutoModelForCausalLM, AutoTokenizer

# We're using OpenLLaMA 3B, an open-source alternative to Meta's LLaMA
# This model is small enough to fine-tune on consumer GPUs
# but still capable of understanding and generating human language
model_id = 'openlm-research/open_llama_3b_v2'
```

## 5. Low-Rank Adaptation (LoRA) Configuration

LoRA is a technique that dramatically reduces the number of trainable parameters:

```python
from peft import LoraConfig, get_peft_model

# Configure LoRA parameters
peft_config = LoraConfig(
    r=64,  # Rank of the update matrices - higher means more capacity but more parameters
    lora_alpha=16,  # Scaling factor for the LoRA adaptation
    lora_dropout=0.1,  # Dropout probability for regularization
    bias="none",  # Whether to train bias parameters
    task_type="CAUSAL_LM",  # The type of task
    # We apply LoRA only to attention modules to minimize parameters
    target_modules=["q_proj", "k_proj", "v_proj", "o_proj"],
)
```

**How LoRA works:**
- Instead of updating all model weights (billions of parameters), LoRA adds small adapter matrices
- These adapters are factorized to have low rank (the 'r' parameter)
- During inference, the adapters can be merged with original weights or kept separate
- This reduces trainable parameters by 10-10,000×, making fine-tuning much more efficient

## 6. Quantization for Memory Efficiency

Quantization reduces the precision of weights to save memory:

```python
from transformers import BitsAndBytesConfig

# Configure 4-bit quantization
bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,  # Use 4-bit precision instead of 16 or 32-bit
    bnb_4bit_quant_type="nf4",  # Use normalized float 4-bit format
    bnb_4bit_use_double_quant=True,  # Apply nested quantization for further memory savings
    bnb_4bit_compute_dtype=torch.float16  # Perform computations in half-precision
)

# Model loading configuration
model_kwargs = dict(
    device_map={"": 0},  # Place model on GPU 0
    trust_remote_code=True,  # Allow executing remote code if needed
    torch_dtype=torch.float16,  # Use half-precision
    use_cache=False,  # Disable KV cache during training (saves memory)
    quantization_config=bnb_config,  # Apply quantization
)
```

**Benefits of 4-bit quantization:**
- Reduces memory usage by 8× compared to FP32
- Allows fitting larger models on limited hardware
- When combined with LoRA, enables fine-tuning of multi-billion parameter models on a single GPU
- Minimal impact on model quality when using techniques like normalized float 4 (nf4)

## 7. Training Configuration

Now we'll set up the training parameters:

```python
from transformers import TrainingArguments
from trl import SFTTrainer

# Training hyperparameters
batch_size = 2  # Batch size per device
gradient_accumulation_steps = 16  # Accumulate gradients across multiple steps
num_train_epochs = 1  # Train for one epoch

# Output directory for model checkpoints
output_dir = "./output/"

# Set up training arguments
training_args = TrainingArguments(
    output_dir=output_dir,  # Directory to save model checkpoints
    num_train_epochs=num_train_epochs,
    per_device_train_batch_size=batch_size,
    per_device_eval_batch_size=batch_size,
    fp16=True,  # Use mixed-precision training
    learning_rate=2e-4,  # Higher than typical because we're training few parameters
    lr_scheduler_type="cosine",  # Cosine learning rate schedule
    warmup_ratio=0.1,  # Warm up learning rate over 10% of training
    gradient_accumulation_steps=gradient_accumulation_steps,
    gradient_checkpointing=True,  # Trade computation for memory
    gradient_checkpointing_kwargs={"use_reentrant": False},
    logging_strategy="steps",
    logging_steps=1,  # Log after every step
    save_strategy="steps",
    save_steps=512 // (batch_size * gradient_accumulation_steps),  # Save periodically
    eval_strategy="steps",  # Changed from "evaluation_strategy" to "eval_strategy"
    eval_steps=512 // (batch_size * gradient_accumulation_steps),  # Evaluate periodically
    report_to="wandb",  # Send metrics to W&B
)

# Import the LLMSampleCB callback from our utils.py file
from utils import LLMSampleCB

# Create the SFT trainer
trainer = SFTTrainer(
    model=model_id,  # The model to fine-tune
    model_init_kwargs=model_kwargs,  # Model initialization arguments
    train_dataset=train_dataset,
    eval_dataset=eval_dataset,
    peft_config=peft_config,  # LoRA configuration
    packing=True,  # Pack multiple sequences into one for efficiency
    max_seq_length=1024,  # Maximum sequence length
    args=training_args,
    formatting_func=create_prompt,  # Function to format examples
)
```

**Key training parameters explained:**
- **learning_rate=2e-4**: Higher than typical full fine-tuning rates (∼1e-5) because we're only training adapter parameters
- **batch_size=2, gradient_accumulation_steps=16**: This gives an effective batch size of 32
- **fp16=True**: Mixed precision training for memory efficiency
- **warmup_ratio=0.1**: Gradually increases learning rate at the start of training
- **gradient_checkpointing=True**: Saves memory by recomputing intermediate activations

## 8. Generation Evaluation Setup

We'll create a version of our test dataset without answers for generation:

```python
# Function to create prompts without answers for generation
def create_prompt_no_answer(row):
    row["output"] = ""  # Remove the answer
    return {"text": create_prompt(row)}  # Format as prompt only

# Create version of test dataset for generation
test_dataset = eval_dataset.map(create_prompt_no_answer)

# Create a W&B callback to log model generations during training
wandb_callback = LLMSampleCB(
    trainer,
    test_dataset,
    num_samples=10,  # Generate 10 examples
    max_new_tokens=256  # Each generation can be up to 256 tokens
)

# Add the callback to the trainer
trainer.add_callback(wandb_callback)
```

**What this callback does:**
1. At evaluation steps, takes prompts from the test dataset
2. Runs them through the current model state
3. Logs the generated outputs to W&B Tables
4. This gives qualitative insights into how the model improves during training

## 9. Training the Model

Now we can start the training process:

```python
# Begin training
trainer.train()

# Properly close the W&B run
wandb.finish()
```

During training, you'll see:
- Loss metrics reported to the terminal
- Metrics and sample generations tracked in the W&B dashboard
- Periodic model checkpoints saved to the output directory

## 10. Analyzing Results in W&B

After training completes, you should analyze your results in the W&B dashboard:

1. **Training metrics**: Look for decreasing loss values
2. **Sample generations**: Examine how generations improve over time
3. **System metrics**: Check GPU utilization and memory usage

The most valuable aspect of W&B is the ability to compare multiple runs with different hyperparameters. Try experimenting with:
- Different LoRA ranks (r values)
- Various learning rates
- Different base models
- Larger subsets of the training data

## Next Steps

Now that you understand the basics:

1. Try fine-tuning on the full dataset
2. Experiment with different LoRA configurations
3. Test other model sizes (7B, 13B)
4. Create a W&B report comparing your experiments
5. Deploy your fine-tuned model for inference

Remember that good experiment tracking becomes even more valuable as you run more variations and need to compare results systematically.
