
# Dropout


DATE:  27-11-24


Tags: [[Regularization]]

# References:




# Content:

Dropout is a regularization method that works well and is vital for reducing overfitting.

Sometimes, the nodes in a neural network create strong dependencies on other nodes, which may lead to overfitting. An example is when a few nodes on a layer do most of the work, and the network ignores all the other nodes. Despite having many nodes on the layer, you only have a small percentage of those nodes contributing to predictions. We call this phenomenon "[co-adaptation](https://machinelearning.wtf/terms/co-adaptation/)," and we can tackle it using Dropout.

During training, Dropout randomly removes a percentage of the nodes, forcing the network to learn in a balanced way. Now every node is on its own and can't rely on other nodes to do their work. They have to work harder by themselves.

Both [TensorFlow](https://www.tensorflow.org/api_docs/python/tf/keras/layers/Dropout) and [PyTorch](https://pytorch.org/docs/stable/generated/torch.nn.Dropout.html) use the same implementation of Dropout that follows this process. If dropout=0.2:

1. It zeros out every node of the layer with the specified probability. In this case, a Dropout with a `0.2` rate will zero out every node with a 20% probability.
2. It scales the remaining nodes to account for the missing values. The scaling factor is `1/(1-rate)`. In this case, if we can substitute the rate by `0.2`, we get that it will scale nodes by `1.25`.


