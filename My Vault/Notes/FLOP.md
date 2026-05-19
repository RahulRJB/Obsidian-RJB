
# FLOP


DATE:  07-05-26


Tags: [[Notes/LLMs|LLMs]], [[compute]], [[Architecture]], [[Hardware]], [[FLOPs]], [[FLOPS]], [[KV Cache]]

# References:




# Content:



## FLOPs vs. FLOPS

Distinction between compute budget and hardware speed is defined by capitalization.

## 1. Definitions

* **FLOPs (Floating-point Operations):** The *total compute budget* required for a task (e.g., training an algorithm or generating a token).
* **FLOPS (Floating-point Operations Per Second):** The *hardware performance speed* (e.g., TFLOPS, PFLOPS, EFLOPS).


Computational and memory cost depends heavily on the data type (precision) being used. Modern accelerators achieve their staggering FLOPS numbers by reducing the precision of the calculations.

* **FP32 (Single Precision):** The traditional standard, taking up 4 bytes per parameter. It offers high precision but is incredibly memory and compute-intensive. Rarely used for full LLM training today.
* **FP16 / BF16 (Half Precision):** Takes up 2 bytes. BF16 (Brain Floating Point) trades fraction precision for a larger exponent range, making it highly robust against the gradient underflow/overflow issues common in Deep Learning. This is the current standard for training.
* **FP8 / INT8 / INT4:** Low-precision formats used primarily for quantization during inference. By dropping weights down to 1 byte or even half a byte, memory bandwidth requirements are slashed, dramatically increasing the effective speed of the memory-bound decoding phase.

---

## 2. Algorithmic Compute: Training vs. Inference

The compute requirements for dense Transformer models scale linearly with parameter count and data.

### Training FLOPs
For a single forward pass, each parameter requires roughly 2 operations (one multiply, one add). The backward pass requires approximately twice the compute of the forward pass to calculate gradients and update weights.
* **Total Operations:** 2 (forward) + 4 (backward) = 6 operations per parameter, per token.

**Formula:**
$$C \approx 6ND$$
> **Where:**
> * $C$ = Total compute in FLOPs
> * $N$ = Number of model parameters
> * $D$ = Number of training tokens

### Inference FLOPs
During text generation, the backward pass is eliminated, reducing the compute per token.

**Formula:**
$$C_{inference} \approx 2N \text{ FLOPs per token}$$

While $C \approx 6ND$ is the baseline for total compute, the *distribution* of that compute changes as sequence lengths grow, and the relationship between $N$ (parameters) and $D$ (data) is governed by scaling laws.

### Chinchilla Scaling
DeepMind's Chinchilla scaling laws demonstrated that for compute-optimal training, model size and training data should be scaled in equal proportions. The generally accepted ratio is approximately 20 training tokens for every 1 model parameter ($D = 20N$). 


---

## 3. The Memory Bottleneck: KV Cache

While theoretical FLOPs are useful for estimating bounds, real-world inference in long-context workflows (like RAG or AI Agents) is heavily constrained by memory bandwidth due to the **KV Cache**. During inference, recalculating the key and value vectors for past tokens at every step would be disastrously inefficient. Instead, we cache them. However, this caching introduces a severe memory footprint problem.

Self-attention scales quadratically with sequence length. As sequences grow, the entire historical KV cache must be loaded from High Bandwidth Memory (HBM) into compute cores for *every* generated token.

This splits processing into two distinct efficiency phases:
1.  **Prompt Processing (Prefill):** The prompt is processed in parallel. This phase is **compute-bound**; the GPU can maximize its TFLOPS capability crunching matrix math.
2.  **Token Generation (Decoding):** Token generation is strictly sequential. This phase is **memory-bound**; the GPU idles while waiting for the KV cache to transfer, operating well below its theoretical peak FLOPS.
### Calculating KV Cache Size
The memory required for the KV cache per token is calculated as:
$$\text{Memory}_{\text{KV}} = 2 \times n_{\text{layers}} \times n_{\text{heads}} \times d_{\text{head}} \times \text{batch} \times \text{seq\_len} \times \text{bytes\_per\_param}$$

*(Note: The '2' accounts for storing both the Key and the Value).*

### Architectural Mitigations
Because a massive KV cache severely limits batch sizes and causes latency bottlenecks, several architectural shifts have become standard:
* **[[Multi-Query Attention]] (MQA):** Multiple query heads share a single Key/Value head. This drastically shrinks the KV cache size, trading a slight drop in model quality for massive gains in inference speed and batching capacity.
* **[[Grouped-Query Attention]] (GQA):** A middle ground where groups of query heads share a KV head (used in Llama models), balancing the speed of MQA with the quality of standard Multi-Head Attention.
* **[[PagedAttention]] (vLLM):** An operating-system-level approach to memory management. It breaks the KV cache into fixed-size blocks (pages), eliminating memory fragmentation and allowing the GPU to serve much larger batches concurrently.

---

## 4. Hardware Evolution: 

### TFLOPS vs. Bandwidth

When evaluating accelerators, raw FLOPS dictate the theoretical ceiling, but memory bandwidth dictates actual decoding utilization.

| Metric | NVIDIA H100 (SXM) | NVIDIA B200 (SXM) |
| :--- | :--- | :--- |
| **FP16 Tensor Cores** | **989** TFLOPS | **2,250** TFLOPS |
| **FP8 Tensor Cores** | **1,979** TFLOPS | **4,500** TFLOPS |
| **Memory Bandwidth** | **3.35** TB/s | **8.0** TB/s |
| **VRAM Capacity** | **80** GB | **192** GB |

> The architectural jump to 8.0 TB/s bandwidth and 192 GB VRAM in the B200 is often more impactful than the raw compute scaling, as it directly mitigates the memory-bound constraints of massive KV caches during the decoding phase.

### MFU and MBU

To truly evaluate how well an LLM utilizes hardware, engineers look past peak TFLOPS and measure two specific ratios:

* **Model FLOPs Utilization (MFU):** The ratio of the *observed* throughput (FLOPs achieved during the run) to the *theoretical peak* throughput of the GPU. Because of communication overhead, memory transfer, and kernel inefficiencies, achieving an MFU above 50% during training is considered highly optimized.
* **Memory Bandwidth Utilization (MBU):** Crucial for the decoding phase of inference. It measures how close the system gets to the theoretical memory bandwidth limit (e.g., the 8.0 TB/s on a B200) when streaming weights and the KV cache into the compute cores.

### The Roofline Model
Hardware performance is often visualized using a Roofline Model, which plots performance against **Arithmetic Intensity** (FLOPs per byte of memory accessed).
* If Arithmetic Intensity is low (like during batch-size=1 token decoding), the model hits the "slanted roof" (memory-bound).
* If Arithmetic Intensity is high (like during the initial prompt prefill or large-batch training), the model hits the "flat roof" (compute-bound).