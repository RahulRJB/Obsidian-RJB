
# Callbacks


DATE:  27-03-25


Tags: [[Notes/LLM Training|LLM Training]]  [[Notes/DL|DL]]

# References:




# Content:

Callbacks are functions that are executed at specific points during the training process. They allow you to monitor and control the training behavior in real-time.  

Here's a breakdown of what callbacks are and their importance:

**Core Concept:**

- **Triggered Functions:**
    - Callbacks are essentially functions that are "called back" or executed by the training loop at predefined events.  
    - These events might include the start or end of an epoch, the beginning or end of a batch, or after a certain number of epochs.  

- **Customizable Actions:**
    - They provide a way to inject custom logic into the training process without modifying the core training loop itself.
    - This makes them highly flexible and reusable.


**Common Uses of Callbacks:**

- **Monitoring Training:**
    - Logging training and validation metrics (loss, accuracy, etc.) to the console or to files.  
        
    - Visualizing training progress using tools like TensorBoard or Weights & Biases.  
        
- **Model Checkpointing:**
    - Saving the model's weights at regular intervals or when a certain condition is met (e.g., best validation accuracy).  
        
    - This allows you to resume training or use the best-performing model.  
        
- **Early Stopping:**
    - Halting training when the validation loss stops improving, preventing overfitting.  
        
- **Learning Rate Scheduling:**
    - Dynamically adjusting the learning rate based on training progress.  
        
- **Custom Behaviors:**
    - Creating custom actions, such as sending email notifications, or altering the training data on the fly.  
        

**Example:**

Imagine you want to save the model's weights every 10 epochs. You could create a callback function that checks the current epoch number and saves the model if it's a multiple of 10.

**Benefits:**

- **Modularity:**
    - Callbacks promote modularity by separating monitoring and control logic from the core training loop.
- **Flexibility:**
    - They offer a flexible way to customize training behavior without modifying the underlying training code.  
        
- **Efficiency:**
    - They can help improve training efficiency by enabling early stopping and other optimization techniques.



