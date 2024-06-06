
# Positional Encoding


DATE:  06-06-24


Tags:

# References:

https://www.youtube.com/watch?v=ZMxVe-HK174&list=PLTl9hO2Oobd97qfWC40gOSU8C0iu0m2l4&index=3




# Content:


- ![[Attachments/Pasted image 20240606124550.png]]
- In order to make sure that we always pass a fixed length Matrix we would pad the sequence with just like a dummy character to make it of the maximum number of words(context length) that is allowed into the Transformer.

- ![[Attachments/Pasted image 20240606124953.png]]
- ![[Attachments/Pasted image 20240606125255.png]]
- ![[Attachments/Pasted image 20240606125915.png]]
- ![[Attachments/Pasted image 20240606130029.png]]
- ![[Attachments/Pasted image 20240606130157.png]]
- ![[Attachments/Pasted image 20240606130317.png]]![[Attachments/Pasted image 20240606130400.png]]
- ![[Attachments/Pasted image 20240606130415.png]]
- ![[Attachments/Pasted image 20240606130610.png]]
- Putting all of it in a class:![[Attachments/Pasted image 20240606130641.png]]
- 