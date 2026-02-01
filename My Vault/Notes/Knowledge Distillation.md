
# Knowledge Distillation


DATE:  01-02-26


Tags:

# References:




# Content:


**Knowledge Distillation** is the process of transferring "knowledge" from a large, complex model (the **Teacher**) to a smaller, more efficient model (the **Student**).

The goal is to create a Student model that is lightweight enough to run on mobile devices or edge hardware while retaining as much accuracy as possible from the heavy Teacher model.

---

## How It Works: The Teacher-Student Framework

Standard training usually involves "hard targets" (e.g., a label is either a "Dog" or a "Cat"). Knowledge distillation uses "soft targets," which provide more nuance about how the Teacher thinks.

### 1. The Teacher Model

You start with a pre-trained, high-performing model (like a massive Transformer or a deep ResNet). When this model looks at an image of a Golden Retriever, its output layer doesn't just say "Dog." It might say:

- **Dog:** 90%
    
- **Cat:** 9%
    
- **Car:** 1%
    

### 2. The "Dark Knowledge"

That 9% for "Cat" is crucial. It tells the Student that this specific dog has features (like fur or ears) that are somewhat similar to a cat, but nothing like a car. This distribution of probabilities is called **Dark Knowledge**.

### 3. Temperature ($T$)

To make these small probabilities easier for the Student to learn, we use a hyperparameter called **Temperature**. By raising $T$ in the Softmax function, we "soften" the probability distribution:

$$q_i = \frac{\exp(z_i / T)}{\sum_j \exp(z_j / T)}$$

As $T$ increases, the output becomes smoother, revealing the hidden relationships between classes that the Teacher has discovered.

---

## The Training Process

The Student model is trained using a composite loss function:

- **Distillation Loss:** The difference between the Student’s soft predictions and the Teacher’s soft predictions.
- **Student Loss:** The standard difference between the Student’s prediction and the actual correct label (ground truth).


NOTE: If working with **LLMs** (like distilling a Llama-3 70B into an 8B), you often skip the ground truth labels entirely and use **Sequence-Level Knowledge Distillation**. In that case, the student simply tries to match the teacher's probability distribution for the next token across the entire vocabulary.