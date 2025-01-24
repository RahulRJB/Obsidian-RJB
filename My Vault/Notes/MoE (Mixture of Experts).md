
# MoE (Mixture of Experts)


DATE:  24-01-25


Tags:  [[Notes/LLMs]]

# References:




# Content:

An MoE, or Mixture of Experts, is an ensemble model that combines several smaller "expert" subnetworks inside the GPT-like decoder architecture. Each subnetwork is said to be responsible for handling different types of tasks or, more concretely, tokens. The idea here is that by using multiple smaller subnetworks instead of one large network, MoEs aim to allocate computational resources more efficiently.

In particular, in Mixtral 8x7B, is to replace each feed-forward module in a transformer architecture with 8 expert layers, as illustrated in the figure below.


![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fb6a8621c-2193-4d5f-b140-e8b669ccbc75_1490x1134.png)

_Annotated transformer architecture from Attention Is All You Need, https://arxiv.org/abs/1706.03762_

"Sparse" in the context of a "Sparse Mixture of Experts" refers to the fact that at any given time, only a subset of the expert layers (typically 1 or 2 out of the 8 in Mixtral 8x7B) are actively used for processing a token.

As illustrated in the figure above, the subnetworks replace the feed-forward module in the LLM. A feed-forward module is essentially a multilayer perceptron. In PyTorch-like pseudocode, it essentially looks like this:

```
class FeedForward(torch.nn.Module):   
    def __init__(self, embed_dim, coef):        
        super().__init__()
        self.layers = nn.Sequential(
            torch.nn.Linear(embed_dim, coef*embed_dim),
            torch.nn.ReLU(),
            torch.nn.Linear(coef*n_embed, embed_dim),
            torch.nn.Dropout(dropout)
        )    

    def forward(self, x):
       return self.layers(x)
```

In addition, there is also a _**Router**_ module (also known as a _gating network_) that redirects each of the token embeddings to the 8 expert feed-forward modules. The outputs from these 8 expert feed-forward layers are then summed.



