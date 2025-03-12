
# Hugging Face


DATE:  12-03-25


Tags: [[Notes/LLMs|LLMs]] [[Notes/Finetuning|Finetuning]]

# References:




# Content: 

**The way  HuggingFace works is that it has base models and has many versions of them where they replace the head of the model for many different tasks**


 We can take 'distilbert-base-uncased' and we're gonna fine-tune it to do sentiment analysis.  
 We can take the label maps for positive and negative sentiment and take our model checkpoint and plug it into this  `AutoModelForSequenceClassification` class available from the Transformers library and easily we import this base model specifically ready to do binary
classification. 

```
model_checkpoint = 'distilbert-base-uncased'
# model_checkpoint = 'roberta-base' # you can alternatively use roberta-base but this model is bigger thus training will take longer

# define label maps
id2label = {0: "Negative", 1: "Positive"}
label2id = {"Negative":0, "Positive":1}

# generate classification model from model_checkpoint
model = AutoModelForSequenceClassification.from_pretrained(
    model_checkpoint, num_labels=2, id2label=id2label, label2id=label2id)
```
