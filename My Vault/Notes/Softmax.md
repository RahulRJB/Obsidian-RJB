
# Softmax


DATE:  10-06-24


Tags:

# References:

https://www.youtube.com/watch?v=wjZofJX0v4M&t=104s


# Content:





- Standard way to transform a random list of numbers into a valid distribution, in a way that the largest values are closest to 1.  Every num is raised to the power of *e*, to make then non-negative and then divided by the sum. Usually the largest num get selected.![[Attachments/Pasted image 20240610170800.png]]
- To add a bit of uncertainty, use [[Temperature]]: It acts as regularization. When T is larger, you give more weight to the lower values, to make distribution a bit more uniform. And when smaller, the bigger value will dominate more aggressively. ![[Attachments/Pasted image 20240610171713.png]]

- [[Logits]]: The raw, unnormalized output of the next word prediction is know as the logits![[Attachments/Pasted image 20240610172250.png]] 