
# LoRA


DATE:  07-10-24


Tags: [[Notes/Finetuning|Finetuning]] [[Notes/Supervised Finetuning|Supervised Finetuning]] [[Notes/QLoRA|QLoRA]]

# References:
https://www.youtube.com/watch?v=t509sv5MT0w&t=61s
https://www.databricks.com/blog/efficient-fine-tuning-lora-guide-llms


# Content:

- ![[Attachments/Pasted image 20241010030814.png]]

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
- 
### Example code 1:

https://github.com/ShawhinT/YouTube-Blog/blob/main/LLMs/fine-tuning/ft-example.ipynb

