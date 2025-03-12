
# QLoRA


DATE:  12-03-25


Tags: [[Notes/LoRA|LoRA]] [[Quantization]]

# References: 




# Content:

The weight matrices in neural networks are made up of floating point numbers of type fp32.
- ![[Attachments/Pasted image 20241010024627.png]]Going down to fp16, we save memory but loose precision.
- ![[Attachments/Pasted image 20241010024831.png]]Memory requirements are even larger as we store the weights+gradient+learning rates.
- ![[Attachments/Pasted image 20241010025107.png]]Half precision still works really well. 
- Some models use mixed precision, i.e diff parts of the network use diff datatypes.
- ![[Attachments/Pasted image 20241010025329.png]]Very low levels of precision don't really work out of the box, however a trend to use quantization which allows you to go very low even only integers and still maintain the model performance.
- ![[Attachments/Pasted image 20241010025459.png]]These methods do not simply drop half of the bits which would lead to an information loss but instead calculate a quantization factor that allows to maintain the levels of precision.
- Less digits = less memory+ faster training




- ![[Attachments/Pasted image 20241018014104.png]][[QLoRA]] now we have the option to combine quantization techniques with LoRA to ultimately reduce the hardware requirements. Here we additionally add 4-bit quantization to the pre-trained model weights that means we don't pass the input through the original model weights but instead through a quantized version of it.


