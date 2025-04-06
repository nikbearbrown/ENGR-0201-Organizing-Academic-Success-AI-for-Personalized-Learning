# LLM Fine-Tuning with Weights & Biases, LoRA, and Quantization

This notebook demonstrates how to fine-tune a 3B parameter Llama model on an instruction dataset using multiple advanced techniques:

1. **Weights & Biases (W&B)**: For experiment tracking and visualization
2. **HuggingFace Transformers**: For model loading and training infrastructure
3. **LoRA (Low-Rank Adaptation)**: For parameter-efficient fine-tuning
4. **Quantization**: For reducing memory requirements

While this notebook uses W&B for experiment tracking, other options include MLflow, TensorBoard, and Neptune.ai.

## Setup and Dependencies

```python
# Install all required libraries
# - wandb: For experiment tracking and visualization
# - transformers: HuggingFace's library for working with transformer models
# - trl: Transformer Reinforcement Learning library for supervised fine-tuning
# - datasets: HuggingFace's library for working with datasets
# - peft: Parameter-Efficient Fine-Tuning library (includes LoRA)
# - bitsandbytes: Library for quantization
# - accelerate: Library for distributed training
# - sentencepiece: Tokenizer backend used by many LLMs
!python -m pip install -U wandb transformers trl datasets "protobuf==3.20.3" evaluate peft bitsandbytes accelerate sentencepiece -qqq

# Download the utils.py file which contains helper functions for the notebook
!wget https://github.com/wandb/edu/raw/main/llm-training-course/colab/utils.py

# What's in utils.py?
# This file contains several important utility functions:
#
# 1. Helper functions for loading datasets and models from W&B artifacts
#    - load_ds_from_artifact: Loads HuggingFace datasets from W&B storage
#    - load_model_from_artifact: Loads models and tokenizers from W&B storage
#
# 2. Model saving and evaluation utilities
#    - save_model: Saves trained models as W&B artifacts
#    - param_count: Counts total and trainable parameters
#    - Accuracy: A simple accuracy metric implementation
#
# 3. The LLMSampleCB class - a custom W&B callback that:
#    - Samples model generations during training
#    - Creates W&B tables with the generations
#    - Updates these tables at evaluation time
#    - This helps visualize how the model's outputs improve during training
#
# 4. Text generation helpers
#    - _generate: Function for generating text from prompts
#
# These utilities make it easier to integrate with W&B for tracking
# experiments and visualizing results, especially the LLMSampleCB class
# which we'll use later in this notebook.
```

## Dataset Preparation & W&B Integration

Before we start training, we need to prepare our dataset and set up experiment tracking. In this section, we:

1. **Initialize W&B for experiment tracking**: Weights & Biases is a platform that helps track machine learning experiments, visualize model performance, and collaborate with team members.

2. **Load instruction data**: We're using an "Alpaca" dataset - a collection of instruction-response pairs formatted for training language models to follow instructions. This specific dataset was created using GPT-4 to generate high-quality examples.

3. **Subsample the data**: For demonstration purposes, we're using a small subset of examples. In a real fine-tuning scenario, you'd typically use the entire dataset.

```python
# Initialize a W&B run with project name and tags for organization
# This creates a new experiment in the W&B dashboard
import wandb
wandb.init(project="alpaca_ft",  # The project name in W&B
           job_type="train",     # The type of job we're running
           tags=["hf_sft_lora", "3b"])  # Tags help categorize experiments

# Understanding W&B Artifacts:
# W&B Artifacts are versioned files or directories tracked in your W&B account
# They help with:
# 1. Dataset versioning - know exactly which data version was used for training
# 2. Model versioning - track model checkpoints with metadata
# 3. Lineage tracking - see how datasets and models connect to experiments
# 4. Reproducibility - easily use the same files across different runs

# Download the Alpaca dataset using W&B Artifacts
# Format: '<username>/<project>/<artifact_name>:<version>'
artifact = wandb.use_artifact('capecape/alpaca_ft/alpaca_gpt4_splitted:v4', type='dataset')
# This downloads the dataset and gives us the path to it
artifact_dir = artifact.download()

# Load the dataset using HuggingFace datasets
from datasets import load_dataset
alpaca_ds = load_dataset("json", data_dir=artifact_dir)

# Subsample for faster training (for demonstration purposes)
# In a real scenario, you'd typically use the full dataset
alpaca_ds['train'] = alpaca_ds['train'].select(range(512))  # Only 512 examples
alpaca_ds['test'] = alpaca_ds['test'].select(range(10))     # Only 10 test examples
```

## Prompt Formatting

When fine-tuning language models on instruction data, how we format the prompts is crucial. The model learns the pattern we provide, so consistency is important.

**Why prompt formatting matters:**
- The model will expect the same format during inference that it saw during training
- Clear separators between instruction and response help the model understand where to generate
- The formatting structure guides the model on how to interpret the input and generate the appropriate output

Here, we define two formatting patterns:
1. A format for instructions without additional input context
2. A format for instructions that include supplementary input data

Each format clearly delineates the instruction, any additional input, and where the response should begin, using consistent markers like "### Instruction:" and "### Response:".

```python
# Define prompt formatting functions
# These create the instruction format the model will learn to follow

# For instructions without additional input
def prompt_no_input(row):
    return ("Below is an instruction that describes a task. "
            "Write a response that appropriately completes the request.\n\n"
            "### Instruction:\n{instruction}\n\n### Response:\n{output}").format_map(row)

# For instructions with additional input context
def prompt_input(row):
    return ("Below is an instruction that describes a task, paired with an input that provides further context. "
            "Write a response that appropriately completes the request.\n\n"
            "### Instruction:\n{instruction}\n\n### Input:\n{input}\n\n### Response:\n{output}").format_map(row)

# Choose the appropriate prompt format based on whether input is provided
def create_prompt(row):
    return prompt_no_input(row) if row["input"] == "" else prompt_input(row)

# Prepare datasets for training and evaluation
train_dataset = alpaca_ds["train"]
eval_dataset = alpaca_ds["test"]
```

## Base Model Selection

The choice of base model significantly impacts the performance, capabilities, and resource requirements of your fine-tuned model.

**About OpenLLaMA:**
- OpenLLaMA is an open-source recreation of Meta's LLaMA architecture
- The 3B version has 3 billion parameters, making it relatively small compared to models like Llama-2 (7B to 70B) or GPT-3.5/4
- It's small enough to fine-tune on a single consumer GPU with the right techniques
- Despite its smaller size, it's capable of understanding and generating human language, making it a good candidate for instruction fine-tuning

We're using this model as our foundation, and we'll apply efficient fine-tuning techniques to adapt it to our specific task without requiring massive computational resources.

```python
# Import necessary libraries for model loading and configuration
import torch
from transformers import AutoModelForCausalLM, AutoTokenizer

# Specify the base model to fine-tune
# OpenLLaMA is an open-source variant of Llama with liberal licensing
model_id = 'openlm-research/open_llama_3b_v2'
```

## Low-Rank Adaptation (LoRA)

Traditional fine-tuning updates all parameters in a model, which can be prohibitively expensive for large language models. Low-Rank Adaptation (LoRA) is a parameter-efficient fine-tuning technique that dramatically reduces the number of trainable parameters.

**How LoRA works:**
1. Instead of updating the original weight matrices directly, LoRA introduces small, trainable "adapter" matrices
2. These adapters are factorized into low-rank matrices (hence the name "Low-Rank" Adaptation)
3. During forward passes, the adapters' outputs are added to the outputs of the original weights
4. Only the adapter parameters are updated during training, while the original model remains frozen

**Key LoRA hyperparameters:**
- **r**: The rank of the low-rank matrices. Lower rank means fewer parameters but less expressive power
- **alpha**: Scaling factor that controls the magnitude of the LoRA adaptation
- **target_modules**: Which layers to apply LoRA to (typically attention layers)
- **lora_dropout**: Regularization to prevent overfitting

LoRA typically reduces trainable parameters by 10-10,000× while maintaining similar performance to full fine-tuning!

```python
# Import LoRA components from PEFT library
from peft import LoraConfig, get_peft_model

# Configure LoRA parameters:
# - r: the rank of the low-rank matrices (smaller = fewer parameters, larger = more capacity)
# - lora_alpha: scaling factor for the LoRA update (usually set to match or be larger than r)
# - lora_dropout: regularization to prevent overfitting
# - target_modules: which layers to apply LoRA to (typically attention layers)
peft_config = LoraConfig(
    r=64,  # The rank of the LoRA matrices (higher = more capacity but more parameters)
    lora_alpha=16,  # Weight scaling factor
    lora_dropout=0.1,  # Dropout probability for regularization
    bias="none",  # Whether to train bias parameters
    task_type="CAUSAL_LM",  # The type of task (causal language modeling)
    # Target only attention projection matrices to reduce parameter count
    target_modules=["q_proj", "k_proj", "v_proj", "o_proj"],
)
```

## Quantization

Quantization is a technique that reduces the precision of the model's weights to use less memory, enabling training and inference of larger models on limited hardware.

**What is quantization?**
- By default, model weights are stored in 32-bit floating point (FP32) format
- Quantization converts these weights to lower precision formats like 16-bit (FP16), 8-bit integers (INT8), or even 4-bit (INT4)
- This reduces memory usage proportionally (e.g., 4-bit uses 8× less memory than 32-bit)

**Types of quantization:**
- **Post-training quantization**: Applied after training is complete (inference-only)
- **Quantization-aware training**: The model is aware of quantization during training
- **Quantized training**: The weights are quantized during training (what we're using)

**Benefits:**
- Fits larger models on limited GPU VRAM
- Often provides faster training and inference
- Can be combined with other memory-saving techniques like LoRA

**Potential drawbacks:**
- May reduce model quality if done incorrectly
- Some operations may not be compatible with quantized weights

Here we're using 4-bit quantization via the BitsAndBytes library, which is specifically designed to maintain good performance while drastically reducing memory requirements.

```python
# Import BitsAndBytes for quantization
from transformers import BitsAndBytesConfig

# Configure 4-bit quantization:
# - 4-bit quantization reduces memory usage by 4x compared to FP16
# - nf4 = normalized float 4, a special format for better quality
# - double quantization further reduces memory with minimal quality loss
bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,  # Use 4-bit quantization instead of 8-bit
    bnb_4bit_quant_type="nf4",  # Use normalized float 4 format (better than standard 4-bit)
    bnb_4bit_use_double_quant=True,  # Apply quantization twice for further memory savings
    bnb_4bit_compute_dtype=torch.float16  # Use float16 for computation
)

# Define model loading parameters
model_kwargs = dict(
    device_map={"" : 0},  # Map the model to GPU 0
    trust_remote_code=True,  # Allow executing remote code (needed for some models)
    # low_cpu_mem_usage=True,  # Reduce CPU memory usage (commented out)
    torch_dtype=torch.float16,  # Use half precision for efficiency
    # use_flash_attention_2=True,  # Use flash attention for faster computation (commented out)
    use_cache=False,  # Disable KV cache for training (saves memory)
    quantization_config=bnb_config,  # Apply the quantization configuration
)
```

## Supervised Fine-Tuning Setup

Supervised Fine-Tuning (SFT) is the process of further training a pre-trained language model on specific examples to align it with desired behaviors, like following instructions or generating particular formats of responses.

**Key components of SFT:**
- **Learning rate**: Typically lower than pre-training to avoid catastrophic forgetting
- **Batch size**: Often smaller due to memory constraints when fine-tuning
- **Gradient accumulation**: Simulates larger batch sizes by accumulating gradients over multiple forward/backward passes
- **Mixed precision training**: Uses lower precision (FP16) for computation to save memory and speed up training
- **Evaluation strategy**: How often to evaluate the model on validation data

**TrainingArguments** configures all these hyperparameters, while the **SFTTrainer** from the TRL (Transformer Reinforcement Learning) library handles the actual training process, including:
- Formatting data samples appropriately
- Handling tokenization
- Managing the training loop
- Logging metrics

The proper configuration of these components is crucial for efficient and effective fine-tuning, especially when working with limited computational resources.

```python
# Import training components
from transformers import TrainingArguments
from trl import SFTTrainer  # Supervised Fine-Tuning Trainer from TRL library

# Define training batch parameters
batch_size = 2  # Very small due to memory constraints
gradient_accumulation_steps = 16  # Accumulate gradients over 16 batches (effective batch size = 32)
num_train_epochs = 1  # Train for just one epoch for demonstration

# Set up training arguments with W&B integration
output_dir = "./output/"
training_args = TrainingArguments(
    num_train_epochs=num_train_epochs,
    output_dir=output_dir,  # Directory to save checkpoints
    per_device_train_batch_size=batch_size,
    per_device_eval_batch_size=batch_size,
    fp16=True,  # Use mixed precision training
    learning_rate=2e-4,  # Higher than typical because we're only training a small subset of parameters
    lr_scheduler_type="cosine",  # Cosine learning rate schedule
    warmup_ratio=0.1,  # Warm up learning rate over 10% of training
    gradient_accumulation_steps=gradient_accumulation_steps,
    gradient_checkpointing=True,  # Trade computation for memory
    gradient_checkpointing_kwargs=dict(use_reentrant=False),  # More stable checkpointing
    evaluation_strategy="epoch",  # Evaluate after each epoch
    logging_strategy="steps",  # Log metrics at each step
    logging_steps=1,  # Log after every step (for demonstration)
    save_strategy="epoch",  # Save the model after each epoch
    report_to="wandb",  # Send metrics to W&B
)

# Import LLMSampleCB - the custom callback for model sample generation
# This is the most important class from utils.py for our visualization needs
from utils import LLMSampleCB

# Understanding LLMSampleCB:
# 1. This custom callback inherits from WandbCallback to extend W&B's logging capabilities
# 2. It takes sample prompts and logs model-generated completions during training
# 3. It creates W&B Tables containing:
#    - The original prompts (test samples without answers)
#    - The generated text from the current model state
#    - The generation parameters used
# 4. It runs automatically during evaluation steps via the on_evaluate method
# 5. This gives you qualitative insights into how your model's capabilities
#    evolve during training, which numeric metrics alone can't provide

# Create the SFT trainer with all our configurations
trainer = SFTTrainer(
    model=model_id,  # The model to fine-tune
    model_init_kwargs=model_kwargs,  # Model initialization arguments
    train_dataset=train_dataset,
    eval_dataset=eval_dataset,
    packing=True,  # Pack multiple sequences into one to improve efficiency
    max_seq_length=1024,  # Maximum sequence length to use
    args=training_args,
    formatting_func=create_prompt,  # Function to format examples into prompts
    peft_config=peft_config,  # LoRA configuration
)

## Evaluation and Sample Generation

Evaluating language models is challenging because traditional metrics like accuracy or loss don't always correlate well with human judgments of quality. This is especially true for generative tasks where many different outputs could be correct.

**Why generate samples during training?**
- Provides qualitative insights that metrics alone don't capture
- Helps detect issues like repetition, hallucination, or refusal to follow instructions
- Shows whether the model is actually improving in ways that matter to users
- Allows for early stopping if the generations are getting worse

The LLMSampleCB class is a custom callback that:
1. Takes prompts from our test dataset (without answers)
2. Periodically runs them through the current model state
3. Logs the generated outputs to W&B tables
4. Links these outputs to the training metrics

This lets us observe both quantitative metrics (loss, accuracy) and qualitative improvements (how well the model follows instructions) throughout training.

```python
# Create a version of the test dataset without answers for generation
def create_prompt_no_anwer(row):
    row["output"] = ""  # Remove the answer
    return {"text": create_prompt(row)}  # Return just the formatted prompt

test_dataset = eval_dataset.map(create_prompt_no_anwer)
```

## Custom W&B Logging

```python
# Create a custom W&B callback to log model generations during training
# This creates a powerful visualization that shows how generated text quality
# improves throughout the training process
wandb_callback = LLMSampleCB(
    trainer,                   # The SFT trainer we created
    test_dataset,              # Dataset containing prompts without answers
    num_samples=10,            # Number of examples to log (10 is good for visualization)
    max_new_tokens=256         # Maximum length of generated text per sample
)

# Why this visualization matters:
# 1. Loss curves only tell you if the model is learning, not WHAT it's learning
# 2. For instruction fine-tuning, we care about qualitative improvements:
#    - Is the model following instructions better?
#    - Is the content more accurate?
#    - Is the formatting improving?
# 3. W&B Tables make it easy to compare generations across training checkpoints
# 4. This gives you early feedback if the training is improving the model
#    in the ways you expect

# Add the callback to the trainer - this connects it to the training loop
trainer.add_callback(wandb_callback)
```

## Training Process

Now that we've configured all the components, we're ready to start the training process. The `train()` method of the SFTTrainer will:

1. **Initialize the model**: Load the base model with our LoRA and quantization configurations
2. **Prepare the datasets**: Tokenize and format our training data
3. **Run the training loop**: Iterate through batches, compute gradients, and update parameters
4. **Log metrics**: Send training and evaluation metrics to W&B
5. **Generate samples**: Periodically generate and log text samples via our custom callback
6. **Save checkpoints**: Store model states according to our save strategy

During training, you'll see metrics being logged to the W&B dashboard in real-time, allowing you to monitor progress and catch any issues early.

Once training completes, we properly close the W&B run with `wandb.finish()` to ensure all data is synced to the W&B servers.

```python
# Start the training process
trainer.train()

# Properly close the W&B run when finished
wandb.finish()
```

## Reviewing W&B Results

After training, you'll want to examine your W&B dashboard to understand how the model performed. Here's what to look for:

### 1. Training Metrics
- **Loss curves**: Look for smooth decreases in training loss
- **Evaluation metrics**: Check if your model is improving on validation data
- **Learning rate**: Confirm your learning rate schedule worked as expected

### 2. Sample Generations
- Open the "sample_predictions" table in your W&B run
- Compare how generations improved over time
- Look for:
  - Better adherence to instructions
  - More coherent and accurate responses
  - Proper formatting and structure

### 3. System Metrics
- GPU utilization and memory usage
- Training speed (samples per second)
- This helps optimize your training setup

### 4. Creating Reports
W&B Reports let you compare multiple runs side-by-side with customizable visualizations:
- Group runs by different hyperparameters
- Compare sample outputs across models
- Share findings with teammates

## Next Steps

Now that you've seen this demonstration with a small sample of data, you could:

1. Train on the full dataset for better results
2. Experiment with different LoRA configurations (r, alpha, target modules)
3. Try different learning rates and batch sizes
4. Test on different base models
5. Create a W&B report to compare your experiments

Remember that tracking experiments with W&B (or other tools) becomes even more valuable as you run more variations of your training process.
