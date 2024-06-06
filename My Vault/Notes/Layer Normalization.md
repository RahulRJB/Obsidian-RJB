
# Layer Normalization


DATE:  06-06-24


Tags:

# References:


- ![[Attachments/Pasted image 20240606132030.png]][[Residual connections]]: To ensure that there is a stronger information signal that
flows through deep networks and this is required because during back propagation, there's a 
Vanishing gradients which means that there is eventually a case where as you keep back propagating the gradient updates become zero and this model stops learning. So to prevent that we kind of induce more stronger signals from the input in different parts of the network



- ![[Attachments/Pasted image 20240606132237.png]]Typically the activations of the neurons will be a wide range of positive and negative values. [[Normalization]] encapsulates these values within a much smaller range and typically centered around 0. This allows for is much more stable training as during the back propagation phase and when we actually perform a gradient step we are taking much more even steps. so it is now easier to learn and hence it is faster and also more stable training to get to the optimal position or these optimal parameter values more consistently and in a quick way.


- ![[Attachments/Pasted image 20240606133126.png]]![[Attachments/Pasted image 20240606133112.png]]![[Attachments/Pasted image 20240606133227.png]]![[Attachments/Pasted image 20240606133248.png]]![[Attachments/Pasted image 20240606133318.png]]



- CODE:  
	- Note: Layer normalization done for the last layer across batches!
	  ![[Attachments/Pasted image 20240606133612.png]]![[Attachments/Pasted image 20240606133736.png]]![[Attachments/Pasted image 20240606133902.png]]
	- Class:![[Attachments/Pasted image 20240606133934.png]]
