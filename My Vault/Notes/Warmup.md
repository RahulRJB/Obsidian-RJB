
# Warmup


DATE:  27-03-25


Tags: [[Notes/LLM Training|LLM Training]]  [[DL]]

# References:




# Content:

"Warmup ratio" helps define the portion of the training process dedicated to the warmup phase. Here's a breakdown:  

**Understanding Warmup:**

- **Learning Rate Warmup:**
    - This technique involves starting the training process with a very low learning rate and gradually increasing it to a target or base learning rate over a specified number of steps or epochs.  
        
    - It helps stabilize training, especially in the early stages, preventing the model from making overly drastic weight adjustments that could lead to instability.  
        

**Warmup Ratio:**

- The warmup ratio expresses the length of the warmup phase as a fraction of the total training duration.
- For example:
    - A warmup ratio of 0.1 means that 10% of the total training steps or epochs will be used for the warmup phase.
    - If you have 1000 training steps, a warmup ratio of 0.1 would mean the learning rate will be increased for the first 100 steps.

**Purpose of the Warmup Ratio:**

- **Defining Warmup Length:**
    - It provides a relative measure to easily adjust the warmup phase based on the overall training length.
- **Hyperparameter Tuning:**
    - The warmup ratio is a hyperparameter that can be tuned to optimize model performance.  
        
    - Finding the right ratio can significantly impact training stability and convergence.



