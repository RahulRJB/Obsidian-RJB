

# Decoder


DATE:  06-06-24


Tags: [[Notes/Transformers|Transformers]]

# References:

[Transformer Decoder coded from scratch (youtube.com)](https://www.youtube.com/watch?v=MqDehUoMk-E&list=PLTl9hO2Oobd97qfWC40gOSU8C0iu0m2l4&index=10)
code:  https://github.com/ajhalthor/Transformer-Neural-Network/blob/main/Transformer_Decoder_EXPLAINED!.ipynb



# Content:
- The way decoding starts in the transformer is, when we finish encoding a sequence, the decoder starts with embedding values for the EOS token. It is a common way to initialize the process of decoding the encoded input sentence. However sometimes you'll see people use SOS for start of sentence or start of sequence to initialize this process.

- So far we've talked about how self-attention helps the Transformer keep track of how words are related within a sentence. Hhowever since We're translating a sentence we also need to keep track of the relationships between the input sentence and the output.

- it's super important for the decoder to keep track of the significant words in the input so the main idea of encoder-decoder attention is to allow the decoder to keep track of the significant words in the input.

- 
![[Attachments/Recording 20240608035015.webm]]

- 
![[Attachments/Recording 20240608035307.webm]]
- ![[Attachments/Pasted image 20240608033720.png]]
- ![[Attachments/Pasted image 20240608035407.png]]


- ![[Attachments/Pasted image 20240608034514.png]]









