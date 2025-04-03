
# Positional Embedding


DATE:  06-06-24


Tags:

# References:

https://www.youtube.com/watch?v=ZMxVe-HK174&list=PLTl9hO2Oobd97qfWC40gOSU8C0iu0m2l4&index=3




# Content:


- ![[Attachments/Pasted image 20240606124550.png]]
- In order to make sure that we always pass a fixed length Matrix we would pad the sequence with just like a dummy character to make it of the maximum number of words(context length) that is allowed into the Transformer.

- ![[Attachments/Pasted image 20240606124953.png]]
- ![[Attachments/Pasted image 20240606125255.png]]
**How are the embeddings added?**

- ![[Attachments/Pasted image 20240607144326.png]]![[Attachments/Pasted image 20240607144432.png]]![[Attachments/Pasted image 20240607144500.png]]
Because the sine and cosine functions are repetitive it's possible that two words might get the same position or y-axis values, however because the functions get wider for larger embedding positions and the more embedding values we have then the wider the functions get, then even with a repeat value here and there we end up with a unique sequence of position values for each word. Thus each input word ends up with a unique sequence of position values







- CODE:
	- ![[Attachments/Pasted image 20240606125915.png]]
	- ![[Attachments/Pasted image 20240606130029.png]]
	- ![[Attachments/Pasted image 20240606130157.png]]![[Attachments/Pasted image 20240606130317.png]]![[Attachments/Pasted image 20240606130400.png]]
	- ![[Attachments/Pasted image 20240606130415.png]]
	- ![[Attachments/Pasted image 20240606130610.png]]
	- Putting all of it in a class:![[Attachments/Pasted image 20240606130641.png]]