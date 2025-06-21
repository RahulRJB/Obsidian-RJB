
# vLLM


DATE:  20-06-25


Tags:  [[Notes/LLMs|LLMs]]  [[inferencing]]

# References:




# Content:

vLLM allows you to run your LLMs faster, serve more users simultaneously, and handle longer sequences, all while potentially reducing your hardware costs. It achieves this through groundbreaking techniques, most notably **PagedAttention** and **continuous batching**.

### The Core Problem: The KV Cache Bottleneck

To understand the brilliance of vLLM, you first need to grasp the primary bottleneck in LLM inference: the Key-Value (KV) cache. During the autoregressive generation process, for each new token, the model must attend to the keys and values of all previous tokens in the sequence. These keys and values are stored in the GPU memory in what's called the KV cache.

The KV cache has two challenging characteristics:

- **It's large:** For long sequences, the KV cache can consume a significant amount of GPU memory.
    
- **It's dynamic:** The size of the KV cache grows with each generated token, making memory management difficult.
    

Traditional LLM serving systems pre-allocate a contiguous block of memory for the KV cache for each request. This approach leads to two major problems:

- **Internal Fragmentation:** Since the exact sequence length is unknown beforehand, a conservative (often maximum) amount of memory is reserved. This leads to a lot of wasted, unused memory within the allocated block.
    
- **External Fragmentation:** Even if there's enough total free memory, it might be scattered in small, non-contiguous chunks. If a new request requires a large, contiguous block for its KV cache, it can't be served, even though there's technically enough memory available.
    

This memory inefficiency severely limits the number of requests that can be processed concurrently (the batch size), thus reducing the overall throughput of the system.

### How vLLM Solves the Problem: PagedAttention and Continuous Batching

vLLM tackles the KV cache bottleneck head-on with two key innovations:

#### 1. PagedAttention: Virtual Memory for the KV Cache

Inspired by the concept of virtual memory and paging in operating systems, **PagedAttention** is a novel attention algorithm that manages the KV cache in a much more efficient way.

Here's a technical breakdown of how it works:

- **Non-Contiguous Memory Allocation:** Instead of allocating a large, contiguous memory block for the KV cache of each sequence, PagedAttention partitions the cache into smaller, fixed-size blocks. These blocks can be stored in non-contiguous locations in the GPU memory.
    
- **Block Tables:** vLLM maintains a "block table" for each sequence. This table maps the logical blocks of the KV cache (as seen by the model) to their corresponding physical blocks in the GPU memory.
    
- **Eliminating Fragmentation:** This approach virtually eliminates memory fragmentation.
    - **Internal Fragmentation:** Since blocks are small and allocated on demand, there's minimal wasted memory within a block.
        
    - **External Fragmentation:** As blocks can be stored anywhere in memory, the system can efficiently utilize all available memory, regardless of how fragmented it is.
- **Efficient Memory Sharing:** A significant advantage of PagedAttention is its ability to enable efficient memory sharing. For instance, in parallel sampling where multiple output sequences are generated from the same prompt, the physical blocks holding the KV cache for the shared prompt can be mapped to the logical blocks of all the output sequences. This is achieved through a copy-on-write mechanism. When a new token needs to be written to a shared block, a new block is created, and the mapping is updated for that specific sequence, leaving the original shared block untouched for others. This can reduce memory usage by up to 55% in such scenarios.
    

The mathematical representation of the attention mechanism is adapted to work with this block-based memory layout, allowing for efficient computation without requiring contiguous memory.

#### 2. Continuous Batching: Maximizing GPU Utilization

Traditional static batching waits for all sequences in a batch to finish generating before moving on to the next batch. This leads to significant GPU idle time, as faster-generating sequences have to wait for the slowest one to complete. This is a classic head-of-line blocking problem.

vLLM implements **continuous batching** (also known as dynamic batching or in-flight batching) to overcome this.

- **Step-Level Scheduling:** Instead of batching at the request level, vLLM batches at the individual forward pass (token generation) level.
- **Dynamic Request Handling:** An incoming request doesn't have to wait for a full batch to form. The vLLM scheduler can add new requests to the running batch as soon as there is available capacity on the GPU.
    
- **No More Waiting:** When a sequence in the batch finishes generating, it is immediately removed, and a new request from the queue can be added.
    

This continuous flow of requests keeps the GPU constantly busy, dramatically increasing throughput and reducing the average latency for individual requests.

### Performance and Benchmarks

The combination of PagedAttention and continuous batching gives vLLM a significant performance edge over other inference engines. Numerous benchmarks have shown that vLLM can achieve:

- **Up to 24x higher throughput** compared to traditional Hugging Face Transformers implementations.
    
- **Significantly lower latency** on average for concurrent requests.
- **The ability to serve a much larger number of concurrent users** on the same hardware.

When compared to other optimized inference servers like Hugging Face's Text Generation Inference (TGI) and NVIDIA's TensorRT-LLM, vLLM often demonstrates superior or competitive performance, especially in scenarios with high concurrency and long sequences. While the exact performance can depend on the specific model, hardware, and workload, vLLM consistently ranks as a top-tier solution.



