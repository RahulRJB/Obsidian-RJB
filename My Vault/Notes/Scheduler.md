
# Scheduler


DATE:  27-03-25


Tags:  [[Notes/LLM Training]] [[DL]]

# References:




# Content:

A scheduler is a technique used to adjust the learning rate during the training process. The learning rate is a crucial hyperparameter that determines the step size taken to update the model's weights based on the calculated gradients.  

Here's a breakdown of what a scheduler does and why it's important:

**Purpose of a Scheduler:**

- **Dynamic Learning Rate Adjustment:**
    - Instead of keeping the learning rate constant throughout training, a scheduler modifies it according to a predefined strategy.  
        
    - This allows for more effective convergence and can lead to better model performance.  
        
- **Improved Convergence:**
    - Initially, a higher learning rate can help the model quickly explore the solution space.  
        
    - Later, as the model approaches an optimal solution, a smaller learning rate enables finer adjustments, preventing overshooting and allowing for precise convergence.  
        
- **Avoiding Local Minima:**
    - Schedulers can help the model escape local minima, which are suboptimal solutions where the model might get stuck.

**Common Scheduler Strategies:**

- **Learning Rate Decay:**
    - Gradually reduces the learning rate over time.  
        
    - Common decay methods include step decay, exponential decay, and cosine decay.  
        
- **Cyclical Learning Rates:**
    - Vary the learning rate cyclically, alternating between higher and lower values.  
        
    - This can help the model explore different regions of the solution space and potentially escape local minima.  
        
- **Adaptive Learning Rate Methods:**
    - Some optimizers, like Adam or AdaGrad, have built-in mechanisms that adapt the learning rate for each parameter based on its historical gradients. These can be considered as a form of learning rate scheduling.  
        
- **One-Cycle Learning Rate Policy:**
    - This Policy increases the learning rate for half of the training, and then decreases it for the other half of the training process.



