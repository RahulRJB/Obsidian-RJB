
# LoRA


DATE:  07-10-24


Tags: [[Notes/Finetuning|Finetuning]] [[Notes/Supervised Finetuning|Supervised Finetuning]] [[Notes/QLoRA|QLoRA]]

# References:
https://www.youtube.com/watch?v=t509sv5MT0w&t=61s
https://www.databricks.com/blog/efficient-fine-tuning-lora-guide-llms


# Content:

- Instead of fine-tuning all parameters in a pre-trained model (which can be billions of parameters), LoRA freezes the original model weights and introduces small trainable "rank decomposition" matrices alongside the original weights. These low-rank matrices create an "adaptation" layer that modifies the behavior of the model.
  The key insight is that updates to large pre-trained models often have a low "intrinsic rank" - meaning you can represent these changes through much smaller matrices that are multiplied together.

- Without LoRA:![[Attachments/Pasted image 20250312132715.png]]
- With LoRA:![[Attachments/Pasted image 20250312133033.png]]
- r: Intrinsic rank of the W0 matrix


- Low rank matrices have several practical applications because they provide a compact representations and reduce complexity.
- LoRA is motivated by a paper published in 2021 by Facebook research that discusses the intrinsic dimensionality of large models. The key point is that there exists a low Dimension re-parametrization that is as effective for fine-tuning as the full parameter space. Basically this means certain downstream tasks don't need to tune all parameters but instead can transform a much smaller set of weights to achieve a similar performance. ![[Attachments/Pasted image 20241010031108.png]]In finetuning BERT, a subset of parameters, ~200, it was possible to achieve 90% of the accuracy of full fine tuning. This is how they define the intrinsic dimension, i.e the number of parameters needed to achieve a certain accuracy.
- ![[Attachments/Pasted image 20241010031258.png]]Another interesting finding is that the larger the model the lower the intrinsic dimension. This means in theory that these large foundation models can be tuned on very few parameters to achieve a good performance and that's mostly because they have already learned a broad set of features and are general purpose models.
- Based on these results of the above paper, LoRA paper presented by Microsoft researchers proposed that the change in model weights, **∆W**, also has a low intrinsic dimension. As we know from before, the dimension is related to the rank of a matrix therefore LoRA suggests to fine-tune through a low rank Matrix.
- B&A initialized such that at the start of training, ∆W=0.  This is done by setting B=0 and the weights of A sampled from a normal distribution.
![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F014307aa-3e4d-47d1-a99f-37892d943c97_1600x702.png)
![[Attachments/Pasted image 20241018014911.png]]![[Attachments/Pasted image 20241018015001.png]]
- In LoRA forward pass, in addition to the regular forward function which we can see on the top, we now also send the inputs through the low rank matrices and scale the result with a scaling Factor. The output of that is simply added to the output of the frozen model. The only trainable parameters are A and B, the low rank matrices.
- The rank r, corresponds to the intrinsic Dimension which means to what extent we want to decompose the matrices. Typical numbers range from 1 to 64 and express the amount of compression on the weights
- Scaling factor **α**, simply controls the amount of change that is added to the original model weights. Therefore it balances the knowledge of the pre-trained model and the adaption to a new task.
- Both the r and α are hyper-parameters. α/r =1 if we want to fully add LoRA. >1 if you want to put more emphasis on the fine tuning weights. If your LoRA model tends to overfit a lower value might help. 
- The reason this scaling, α/r is added because it helps to stabilize other hyper parameters like learning rates.  So in practice you might want to try different levels of decomposition by varying the rank and through the scaling you don't have to tweak the other parameters too much. 
- Increasing the rank does not necessarily improve the performance. Most likely because the data has a small intrinsic rank. But this certainly depends on the data set a good question to ask when choosing the rank is did the foundation model already see similar data or is my data set substantially different.


### Example [[code]] 1:

https://github.com/ShawhinT/YouTube-Blog/blob/main/LLMs/fine-tuning/ft-example.ipynb

### Example [[code]] 2:  

Comprehensive, industry-ready Python implementation of LoRA fine-tuning for an LLM.
- #### Key Features:
	- **Flexible Configuration**: Command-line arguments for all important parameters, including model selection, LoRA hyperparameters, and training settings.
	- **Model Compatibility**: Automatic detection of target modules based on model architecture (LLaMA, Mistral, GPT-2, Bloom, etc.)
	- **Quantization Support**: Options for 8-bit and 4-bit quantization to reduce memory requirements for large models.
	- **Data Handling**: Support for both Hugging Face datasets and custom data files.
	- **Training Optimizations**:
	    - Mixed precision (fp16) training
	    - Gradient accumulation
	    - Learning rate warmup
	    - Checkpointing and evaluation during training
	- **Monitoring and Logging**:
	    - Optional Weights & Biases integration
	    - Detailed logging
	    - Progress tracking
	- **Evaluation**: Generates sample outputs for quick quality assessment.
	- **Model Storage**:
	    - Option to push to Hugging Face Hub
	    - Ability to merge LoRA weights with base model for deployment.

```
import os
import torch
import numpy as np
from datasets import load_dataset
from transformers import (
    AutoModelForCausalLM,
    AutoTokenizer,
    TrainingArguments,
    Trainer,
    DataCollatorForLanguageModeling,
    set_seed
)
from peft import (
    LoraConfig,
    TaskType,
    get_peft_model,
    prepare_model_for_kbit_training,
    PeftModel
)
import wandb
import argparse
from typing import Dict, List, Optional
from tqdm import tqdm
import logging

# Set up logging
logging.basicConfig(
    format="%(asctime)s - %(levelname)s - %(name)s - %(message)s",
    datefmt="%m/%d/%Y %H:%M:%S",
    level=logging.INFO
)
logger = logging.getLogger(__name__)


def parse_args():
    parser = argparse.ArgumentParser(description="Fine-tune an LLM with LoRA")
    
    # Model arguments
    parser.add_argument("--base_model", type=str, required=True, help="Path to base model or model identifier from huggingface.co/models")
    parser.add_argument("--tokenizer_name", type=str, default=None, help="Pretrained tokenizer name or path if not the same as model_name")
    
    # LoRA configuration
    parser.add_argument("--lora_r", type=int, default=8, help="Lora attention dimension")
    parser.add_argument("--lora_alpha", type=int, default=16, help="Lora alpha parameter")
    parser.add_argument("--lora_dropout", type=float, default=0.05, help="Dropout probability for LoRA layers")
    parser.add_argument("--target_modules", type=str, default=None, help="Comma-separated list of module names to apply LoRA to")
    
    # Data arguments
    parser.add_argument("--dataset_name", type=str, default=None, help="The name of the dataset to use")
    parser.add_argument("--dataset_config_name", type=str, default=None, help="The configuration name of the dataset to use")
    parser.add_argument("--train_file", type=str, default=None, help="Path to the training data file")
    parser.add_argument("--validation_file", type=str, default=None, help="Path to the validation data file")
    parser.add_argument("--text_column", type=str, default="text", help="Column containing text data")
    parser.add_argument("--max_seq_length", type=int, default=512, help="Maximum sequence length")
    
    # Training arguments
    parser.add_argument("--output_dir", type=str, default="./lora_model", help="Output directory")
    parser.add_argument("--num_train_epochs", type=int, default=3, help="Number of training epochs")
    parser.add_argument("--per_device_train_batch_size", type=int, default=8, help="Batch size per device during training")
    parser.add_argument("--per_device_eval_batch_size", type=int, default=8, help="Batch size per device during evaluation")
    parser.add_argument("--gradient_accumulation_steps", type=int, default=1, help="Number of updates steps to accumulate before performing a backward/update pass")
    parser.add_argument("--learning_rate", type=float, default=3e-4, help="Initial learning rate")
    parser.add_argument("--weight_decay", type=float, default=0.0, help="Weight decay to apply")
    parser.add_argument("--warmup_ratio", type=float, default=0.03, help="Ratio of total training steps used for warmup")
    parser.add_argument("--logging_steps", type=int, default=10, help="Log every X updates steps")
    parser.add_argument("--eval_steps", type=int, default=100, help="Run evaluation every X steps")
    parser.add_argument("--save_steps", type=int, default=1000, help="Save checkpoint every X updates steps")
    parser.add_argument("--save_total_limit", type=int, default=3, help="Limit the total amount of checkpoints")
    
    # Other arguments
    parser.add_argument("--seed", type=int, default=42, help="Random seed")
    parser.add_argument("--push_to_hub", action="store_true", help="Push model to the hub")
    parser.add_argument("--hub_model_id", type=str, default=None, help="Model ID for uploading to the hub")
    parser.add_argument("--use_8bit", action="store_true", help="Use 8-bit quantization to reduce memory usage")
    parser.add_argument("--use_4bit", action="store_true", help="Use 4-bit quantization to further reduce memory usage")
    parser.add_argument("--use_wandb", action="store_true", help="Log training with wandb")
    parser.add_argument("--wandb_project", type=str, default="lora-finetuning", help="wandb project name")
    
    args = parser.parse_args()
    
    # Validate input arguments
    if args.dataset_name is None and args.train_file is None:
        raise ValueError("Need either a dataset name or a training file.")
    
    # Infer target modules if not specified
    if args.target_modules is None:
        # Default target modules for different model architectures
        if "llama" in args.base_model.lower() or "mistral" in args.base_model.lower():
            args.target_modules = "q_proj,v_proj,k_proj,o_proj"
        elif "gpt-neo" in args.base_model.lower():
            args.target_modules = "q_proj,v_proj,k_proj,out_proj"
        elif "gpt2" in args.base_model.lower():
            args.target_modules = "c_attn,c_proj"
        elif "bloom" in args.base_model.lower():
            args.target_modules = "query_key_value,dense"
        elif "falcon" in args.base_model.lower():
            args.target_modules = "query_key_value,dense"
        else:
            logger.warning(
                "No default target modules specified for this model. "
                "You should specify --target_modules explicitly."
            )
            args.target_modules = ""
    
    # Convert target_modules from string to list
    if args.target_modules:
        args.target_modules = args.target_modules.split(",")
    else:
        args.target_modules = []
        
    return args


def load_and_prepare_model(args):
    """Load and prepare the model with LoRA configuration."""
    logger.info(f"Loading base model: {args.base_model}")
    
    # Determine quantization config
    quantization_config = None
    load_in_8bit = args.use_8bit
    load_in_4bit = args.use_4bit
    
    # Ensure we're not trying to use both 4-bit and 8-bit
    if load_in_8bit and load_in_4bit:
        logger.warning("Both 4-bit and 8-bit quantization specified. Using 4-bit.")
        load_in_8bit = False
    
    # Load the model with appropriate quantization
    model = AutoModelForCausalLM.from_pretrained(
        args.base_model,
        load_in_8bit=load_in_8bit,
        load_in_4bit=load_in_4bit,
        torch_dtype=torch.float16,
        device_map="auto",
    )
    
    # Load tokenizer
    tokenizer_name = args.tokenizer_name if args.tokenizer_name else args.base_model
    tokenizer = AutoTokenizer.from_pretrained(tokenizer_name)
    
    # Set pad token if needed
    if tokenizer.pad_token is None:
        tokenizer.pad_token = tokenizer.eos_token
        logger.info(f"Setting pad_token to eos_token: {tokenizer.pad_token}")
    
    # Prepare model for k-bit training if using quantization
    if load_in_8bit or load_in_4bit:
        logger.info("Preparing model for k-bit training")
        model = prepare_model_for_kbit_training(model)
    
    # Configure LoRA
    logger.info(f"Configuring LoRA with rank={args.lora_r}, alpha={args.lora_alpha}")
    logger.info(f"Target modules: {args.target_modules}")
    
    peft_config = LoraConfig(
        task_type=TaskType.CAUSAL_LM,
        r=args.lora_r,
        lora_alpha=args.lora_alpha,
        lora_dropout=args.lora_dropout,
        target_modules=args.target_modules,
        bias="none",
        inference_mode=False,
    )
    
    # Create the LoRA model
    model = get_peft_model(model, peft_config)
    
    # Print trainable parameters info
    model.print_trainable_parameters()
    
    return model, tokenizer


def prepare_dataset(args, tokenizer):
    """Load and preprocess the dataset for training."""
    # Load dataset
    if args.dataset_name is not None:
        logger.info(f"Loading dataset: {args.dataset_name}")
        raw_datasets = load_dataset(
            args.dataset_name,
            args.dataset_config_name,
        )
    else:
        data_files = {}
        if args.train_file is not None:
            data_files["train"] = args.train_file
        if args.validation_file is not None:
            data_files["validation"] = args.validation_file
        extension = args.train_file.split(".")[-1] if args.train_file else ""
        raw_datasets = load_dataset(extension, data_files=data_files)
    
    # Split datasets
    if "validation" not in raw_datasets and "train" in raw_datasets:
        logger.info("Creating validation split from training data")
        raw_datasets = raw_datasets["train"].train_test_split(test_size=0.1, seed=args.seed)
        raw_datasets = {
            "train": raw_datasets["train"],
            "validation": raw_datasets["test"]
        }
    
    # Preprocessing function
    def preprocess_function(examples):
        # Tokenize texts
        texts = examples[args.text_column]
        model_inputs = tokenizer(
            texts,
            max_length=args.max_seq_length,
            padding="max_length",
            truncation=True,
            return_tensors="pt",
        )
        
        # Create language modeling labels (shifted input_ids)
        model_inputs["labels"] = model_inputs["input_ids"].clone()
        
        return model_inputs
    
    # Apply preprocessing
    logger.info("Preprocessing datasets")
    processed_datasets = {}
    
    for split in raw_datasets:
        logger.info(f"Processing {split} split")
        processed_datasets[split] = raw_datasets[split].map(
            preprocess_function,
            batched=True,
            remove_columns=raw_datasets[split].column_names,
            desc=f"Preprocessing {split} dataset",
            num_proc=4,
        )
    
    return processed_datasets


def train_model(args, model, tokenizer, processed_datasets):
    """Train the model with LoRA."""
    # Initialize wandb if specified
    if args.use_wandb:
        wandb.init(project=args.wandb_project, name=f"lora-{args.base_model.split('/')[-1]}")
    
    # Define training arguments
    training_args = TrainingArguments(
        output_dir=args.output_dir,
        overwrite_output_dir=True,
        num_train_epochs=args.num_train_epochs,
        per_device_train_batch_size=args.per_device_train_batch_size,
        per_device_eval_batch_size=args.per_device_eval_batch_size,
        gradient_accumulation_steps=args.gradient_accumulation_steps,
        learning_rate=args.learning_rate,
        weight_decay=args.weight_decay,
        warmup_ratio=args.warmup_ratio,
        logging_dir=f"{args.output_dir}/logs",
        logging_steps=args.logging_steps,
        evaluation_strategy="steps",
        eval_steps=args.eval_steps,
        save_strategy="steps",
        save_steps=args.save_steps,
        save_total_limit=args.save_total_limit,
        load_best_model_at_end=True,
        report_to="wandb" if args.use_wandb else None,
        push_to_hub=args.push_to_hub,
        hub_model_id=args.hub_model_id,
        seed=args.seed,
        fp16=True,  # Use mixed precision training
    )
    
    # Create data collator for language modeling
    data_collator = DataCollatorForLanguageModeling(
        tokenizer=tokenizer,
        mlm=False,  # We're not doing masked language modeling
    )
    
    # Initialize Trainer
    trainer = Trainer(
        model=model,
        args=training_args,
        train_dataset=processed_datasets["train"],
        eval_dataset=processed_datasets["validation"],
        data_collator=data_collator,
    )
    
    # Train the model
    logger.info("Starting training")
    trainer.train()
    
    # Save the final model
    logger.info(f"Saving final model to {args.output_dir}")
    model.save_pretrained(args.output_dir)
    tokenizer.save_pretrained(args.output_dir)
    
    # Push to hub if specified
    if args.push_to_hub:
        trainer.push_to_hub()
    
    return model


def evaluate_model(model, tokenizer, processed_datasets):
    """Evaluate the fine-tuned model."""
    logger.info("Evaluating the fine-tuned model")
    
    # Use a small subset for evaluation
    eval_dataset = processed_datasets["validation"].select(range(min(100, len(processed_datasets["validation"]))))
    
    # Evaluation function
    def generate_and_evaluate(example):
        # Get the first 50 tokens as prompt
        input_ids = example["input_ids"][:50]
        prompt = tokenizer.decode(input_ids, skip_special_tokens=True)
        
        # Generate continuation
        inputs = tokenizer(prompt, return_tensors="pt").to(model.device)
        with torch.no_grad():
            outputs = model.generate(
                inputs.input_ids,
                max_length=100,
                do_sample=True,
                top_p=0.95,
                temperature=0.7,
                num_return_sequences=1
            )
        
        generated_text = tokenizer.decode(outputs[0], skip_special_tokens=True)
        return {"prompt": prompt, "generated": generated_text}
    
    # Apply evaluation
    results = []
    for example in tqdm(eval_dataset, desc="Evaluating"):
        results.append(generate_and_evaluate(example))
    
    return results


def merge_and_save_lora_weights(args):
    """Merge LoRA weights with the base model and save the full model."""
    logger.info("Merging LoRA weights with base model")
    
    # Load base model
    base_model = AutoModelForCausalLM.from_pretrained(
        args.base_model,
        torch_dtype=torch.float16,
        device_map="auto",
    )
    
    # Load LoRA model
    lora_model = PeftModel.from_pretrained(
        base_model,
        args.output_dir,
        torch_dtype=torch.float16,
        device_map="auto",
    )
    
    # Merge weights
    merged_model = lora_model.merge_and_unload()
    
    # Save merged model
    merged_output_dir = f"{args.output_dir}_merged"
    os.makedirs(merged_output_dir, exist_ok=True)
    merged_model.save_pretrained(merged_output_dir)
    
    # Save tokenizer
    tokenizer = AutoTokenizer.from_pretrained(args.base_model)
    tokenizer.save_pretrained(merged_output_dir)
    
    logger.info(f"Merged model saved to {merged_output_dir}")
    
    return merged_output_dir


def main():
    # Parse command line arguments
    args = parse_args()
    
    # Set random seed for reproducibility
    set_seed(args.seed)
    
    # Load and prepare model
    model, tokenizer = load_and_prepare_model(args)
    
    # Prepare dataset
    processed_datasets = prepare_dataset(args, tokenizer)
    
    # Train the model
    trained_model = train_model(args, model, tokenizer, processed_datasets)
    
    # Evaluate model with some generation examples
    evaluation_results = evaluate_model(trained_model, tokenizer, processed_datasets)
    
    # Print a few generation examples
    logger.info("Generation examples:")
    for i, result in enumerate(evaluation_results[:3]):
        logger.info(f"Example {i+1}")
        logger.info(f"Prompt: {result['prompt']}")
        logger.info(f"Generated: {result['generated']}")
        logger.info("-" * 50)
    
    # Optionally merge and save the full model
    if input("Merge LoRA weights with base model? (y/n): ").lower() == "y":
        merged_output_dir = merge_and_save_lora_weights(args)
        logger.info(f"Full merged model saved to {merged_output_dir}")
    
    logger.info("Fine-tuning complete!")


if __name__ == "__main__":
    main()
```

Running via terminal:

```
python lora_finetune.py \
  --base_model "meta-llama/Llama-2-7b-hf" \
  --train_file "path/to/training_data.json" \
  --validation_file "path/to/validation_data.json" \
  --output_dir "./lora_llama2" \
  --lora_r 16 \
  --lora_alpha 32 \
  --lora_dropout 0.05 \
  --learning_rate 2e-4 \
  --num_train_epochs 3 \
  --per_device_train_batch_size 4 \
  --gradient_accumulation_steps 4 \
  --use_8bit \
  --use_wandb
```
