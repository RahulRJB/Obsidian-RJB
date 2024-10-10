
# LoRA


DATE:  07-10-24


Tags: [[Notes/Finetuning|Finetuning]]

# References:
https://www.youtube.com/watch?v=t509sv5MT0w&t=61s



# Content:
- The weight matrices in neural networks are made up of floating point numbers of type fp32.
- ![[Attachments/Pasted image 20241010024627.png]]Going down to fp16, we save memory but loose precision.
- ![[Attachments/Pasted image 20241010024831.png]]Memory requirements are even larger as we store the weights+gradient+learning rates.
- ![[Attachments/Pasted image 20241010025107.png]]Half precision still works really well. 
- Some models use mixed precision, i.e diff parts of the network use diff datatypes.
- ![[Attachments/Pasted image 20241010025329.png]]Very low levels of precision don't really work out of the box, however a trend to use quantization which allows you to go very low even only integers and still maintain the model performance.
- ![[Attachments/Pasted image 20241010025459.png]]These methods do not simply drop half of the bits which would lead to an information loss but instead calculate a quantization factor that allows to maintain the levels of precision.
- Less digits = less memory+ faster training
- ### Finetuning Approaches:
	- ![[Attachments/Pasted image 20241010030020.png]]
	- ![[Attachments/Pasted image 20241010030421.png]]A different idea, specifically designed for language models is prefix tuning. This is a very lightweight alternative to fine-tuning which simply optimizes the input Vector for language models. Essentially this is a way of prompting by prepending specific vectors to the input for a model the idea is to add context to steer the language model.
	- LoRA
- ![[Attachments/Pasted image 20241010030814.png]]
- Low rank matrices have several practical applications because they provide a compact representations and reduce complexity
- LoRA is motivated by a paper published in 2021 by Facebook research that discusses the intrinsic dimensionality of large models. The key point is that there exists a low Dimension re-parametrization that is as effective for fine-tuning as the full parameter space. Basically this means certain downstream tasks don't need to tune all parameters but instead can transform a much smaller set of weights to achieve a similar performance
- ![[Attachments/Pasted image 20241010031108.png]]In finetuning BERT, a subset of parameters, ~200, it was possible to achieve 90% of the accuracy of full fine tuning using a certain threshold is

how they Define the intrinsic Dimension

so basically the number of parameters

needed to achieve a certain accuracy.
- ![[Attachments/Pasted image 20241010031258.png]]another interesting finding evaluated on

different data sets is that the larger

the model the lower the intrinsic

dimension

this means in theory that these large

Foundation models can be tuned on very

few parameters to achieve a good

performance and that's mostly because

they already learned a broad set of

features and our general purpose models
- based on these results the Laura paper

## Rank decomposition

presented by Microsoft researchers

proposes the idea that the change in

model weights Delta w

also has a low intrinsic dimension

as we know from before the dimension is

related to the rank of a matrix

therefore Laura suggests to fine-tune

through a low rank Matrix.






