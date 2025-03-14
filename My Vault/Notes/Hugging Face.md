
# Hugging Face


DATE:  12-03-25


Tags: [[Notes/LLMs|LLMs]] [[Notes/Finetuning|Finetuning]]

# References:
[[code]]



# Content: 

#### Running: 
  1. load model from pretrained for a given task type eg. `AutoModelForCausalLM.from_pretrained()` OR
     `AutoModelForSequenceClassification.from_pretrained()`-> 
     Can load model in quantized form OR model can be quantized after loading -> `prepare_model_for_kbit_training` if using quantization -> 
     `LoraConfig` with tasktype, rank, alpha, dropout, target modules ->
     Create the LoRA model using `get_peft_model`
     
  2. load tokenizer from pretrained eg. `AutoTokenizer.from_pretrained()`-> 
     Set pad_token for the tokenizer -> 
     
  3. Load raw dataset using `load_dataset()` ->
     split dataset ->
     tokenize input data using `tokenizer()`
     
  4. Start training using model, tokenizer, processed dataset ->
     `TrainingArguments()` to set training arguments -> 
     `DataCollatorForLanguageModeling()` is initialised to be used for training - It prepares optimized batches of data for training. Dynamic pads the sequences to the same length within a batch -> 
     `Trainer()` to create trainer using model, training_args, validation/training dataset, data collator ->
     Set for training ->
     Save trained model and tokenizer
     
  5. Evaluate results ->
     `merge_and_save_lora_weights()`

#### Loading Model:
**The way  HuggingFace works is that it has base models and has many versions of them where they replace the head of the model for many different tasks**

 eg. 
```
model_checkpoint = 'distilbert-base-uncased'
id2label = {0: "Negative", 1: "Positive"}
label2id = {"Negative":0, "Positive":1}

# generate classification model from model_checkpoint
model = AutoModelForSequenceClassification.from_pretrained(
    model_checkpoint, num_labels=2, id2label=id2label, label2id=label2id)
```
------------------------------------------------------------------------
```
model = AutoModelForCausalLM.from_pretrained( args.base_model, load_in_8bit=True, 
torch_dtype=torch.float16, 
device_map="auto", 
)
```


#### Data Collator:
The data collator's main job is to take individual examples from your dataset and combine them into optimized batches for training. For language modeling specifically, it handles:

1. Dynamic padding of sequences to the same length within a batch
2. Creating the appropriate attention masks
3. Preparing the labels for language modeling objectives
```
data_collator = DataCollatorForLanguageModeling(
    tokenizer=tokenizer,
)
```
- We pass the tokenizer so it knows how to handle padding and special tokens
- We set `mlm=False` because we're doing causal language modeling (predicting the next token) rather than masked language modeling (predicting masked tokens)

When called during training, the collator:

1. Takes a list of examples (each with `input_ids`, etc.)
2. Pads shorter sequences to match the longest sequence in the batch
3. Creates appropriate attention masks (1 for real tokens, 0 for padding)