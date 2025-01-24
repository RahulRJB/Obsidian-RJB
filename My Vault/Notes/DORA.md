
# DORA


DATE:  24-01-25


Tags: [[Notes/LoRA|LoRA]]

# References:




# Content:

In [DoRA: Weight-Decomposed Low-Rank Adaptation](https://arxiv.org/abs/2402.09353) (February 2024), we extend LoRA by first decomposing a pretrained weight matrix into two parts: a magnitude vector m and a directional matrix _V_. This decomposition is rooted in the idea that any vector can be represented by its length (magnitude) and direction (orientation), and here we apply it to each column vector of a weight matrix. Once we have m and _V_, DoRA applies LoRA-style low-rank updates only to the directional matrix _V_, while allowing the magnitude vector m to be trained separately.

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fe39fff89-8c1b-4e06-80c9-f2ca375af019_1600x1165.png)


_Annotated illustration from the DoRA paper (https://arxiv.org/abs/2402.09353)_

This two-step approach gives DoRA more flexibility than standard LoRA. Rather than uniformly scaling both magnitude and direction as LoRA tends to do, DoRA can make subtle directional adjustments without necessarily increasing the magnitude. The result is improved performance and robustness, as DoRA can outperform LoRA even when using fewer parameters and is less sensitive to the choice of rank.


