
# Quantization


DATE:  13-03-25


Tags: [[Notes/LLMs|LLMs]] 

# References:




# Content:

Quantization is a technique used to reduce the precision of numerical values, typically converting higher precision formats (like 32-bit floating point) to lower precision formats (like 8-bit integers). It's essentially a compression method that trades off some numerical precision for significant reductions in memory usage and computational requirements.

Here's how quantization works in the context of Large Language Models (LLMs):

1. **Weight Representation**: LLMs have billions of parameters (weights) typically stored as 32-bit floating point numbers (FP32). These require significant memory.
2. **Precision Reduction**: Quantization converts these high-precision weights to lower precision formats:
    - FP16 (16-bit floating point)
    - INT8 (8-bit integer)
    - INT4 (4-bit integer)
    - Or even lower bit-widths in some cases
3. **Mapping Process**: The quantization process typically involves:
    - Analyzing the distribution of weights
    - Establishing a scaling factor to map the range of original values to the smaller range
    - Converting each value according to this mapping
4. **Methods**:
    - **Post-training quantization (PTQ)**: Applied after a model is fully trained
    - **Quantization-aware training (QAT)**: Incorporates quantization effects during training

## Benefits in LLMs

1. **Reduced Memory Footprint**:
    - A model using INT8 instead of FP32 requires ~4x less memory
    - This allows larger models to fit on consumer hardware
2. **Faster Inference**:
    - Lower precision calculations can be executed more efficiently
    - Many modern CPUs and GPUs have specialized hardware for low-precision operations
3. **Lower Energy Consumption**:
    - Less memory access and simpler computations reduce power requirements
    - Critical for mobile devices and edge deployment
4. **Reduced Bandwidth Requirements**:
    - Smaller models can be distributed more easily
    - Important for deployment scenarios with limited connectivity
5. **Enabling Local Deployment**:
    - Allows running large models on consumer devices without cloud connectivity
    - Improves privacy and reduces latency


## Pre-Quantization Analysis

Before quantization, you should:

1. **Calibration**: Collect activation statistics by running representative data through your model
2. **Weight Distribution Analysis**: Analyze the distribution of weights to determine optimal quantization parameters

## Quantization Methods

### 1. Post-Training Quantization (PTQ)

```
import torch
from torch.quantization import quantize_dynamic, per_channel_quantize_dynamic
import transformers

# Load your pretrained model
model = transformers.AutoModelForCausalLM.from_pretrained("your-model-path")

# For simple dynamic quantization (weights only)
quantized_model = quantize_dynamic(
    model, 
    {torch.nn.Linear}, # quantize only linear layers
    dtype=torch.qint8
)

# Save the quantized model
quantized_model.save_pretrained("your-quantized-model-path")
```
### 2. Weight-Only Quantization with GPTQ or AWQ

GPTQ (Generative Pretrained Transformer Quantization) and AWQ (Activation-aware Weight Quantization) are specialized methods for transformer models:

```
# Example using GPTQ with optimum library
from optimum.gptq import GPTQQuantizer

quantizer = GPTQQuantizer(
    bits=4,  # 4-bit quantization
    dataset="c4",  # calibration dataset
    block_name_to_quantize="model.layers",
    model_seqlen=2048
)

# Quantize the model
quantized_model = quantizer.quantize_model(model, calibration_examples)
```
### 3. Quantization-Aware Training (QAT)

```
import torch.quantization as quantization

# Create quantization configuration
qconfig = quantization.get_default_qat_qconfig('fbgemm')

# Prepare model for QAT
model.qconfig = qconfig
torch.quantization.prepare_qat(model, inplace=True)

# Fine-tune with quantization awareness
# ... training loop ...

# Convert to quantized model
quantized_model = torch.quantization.convert(model, inplace=False)
```


## Technical Implementation Details

### Linear Layer Quantization

For a linear layer `y = Wx + b`:

**Weight Quantization**:

	def quantize_weights(W, scale, zero_point, bits=8):
		# Compute quantization parameters
		qmin, qmax = 0, 2**bits - 1
		
		# Scale to the quantized range
		W_q = torch.round(W / scale) + zero_point
		
		# Clamp to ensure values are in range
		W_q = torch.clamp(W_q, qmin, qmax)
		
		# Store as integer
		W_q = W_q.to(torch.uint8 if bits == 8 else torch.int4)
		
		return W_q, scale, zero_point
		
	def dequantize_weights(W_q, scale, zero_point):
		# Convert back to floating point during inference
		return scale * (W_q.float() - zero_point)


**Scale/Zero-Point Computation**:

	def compute_scale_zp(W, bits=8):
		qmin, qmax = 0, 2**bits - 1
		
		# AbsMax quantization
		absmax = torch.max(torch.abs(W))
		scale = absmax / (qmax - qmin) * 2
		zero_point = 0  # Symmetric quantization around zero
		
		# Alternatively for asymmetric:
		# wmin, wmax = W.min(), W.max()
		# scale = (wmax - wmin) / (qmax - qmin)
		# zero_point = qmin - torch.round(wmin / scale)
		
		return scale, zero_point


### Per-Channel vs Per-Tensor Quantization

Per-channel quantization typically offers better accuracy:

	def per_channel_quantize(W, bits=8, axis=0):
		# Quantize each output channel separately
		scales = []
		zero_points = []
		W_q = torch.zeros_like(W, dtype=torch.uint8)
		
		for i in range(W.size(axis)):
			if axis == 0:
				w_slice = W[i, :]
			else:
				w_slice = W[:, i]
				
			scale, zp = compute_scale_zp(w_slice, bits)
			w_q_slice = torch.round(w_slice / scale) + zp
			
			# Store back
			if axis == 0:
				W_q[i, :] = w_q_slice
			else:
				W_q[:, i] = w_q_slice
				
			scales.append(scale)
			zero_points.append(zp)
			
		return W_q, torch.tensor(scales), torch.tensor(zero_points)


## Practical Implementation Tips

1. **Layer Selection**: Not all layers need the same precision. Consider keeping attention layers at higher precision than MLP layers.
2. **Block-wise Quantization**: Process the model in blocks to manage memory usage.
3. **Activation Quantization**: Consider the impact of quantizing activations (more complex but higher compression).
4. **Evaluation**: Always evaluate performance on key metrics after quantization.
5. **Integration with Inference Frameworks**: For deployment, integrate with optimized inference frameworks like ONNX Runtime, TensorRT, or specialized libraries like llamacpp.

## Trade-offs

The primary trade-off is some reduction in model accuracy or performance, though modern techniques have made this increasingly minimal. For many applications, a carefully quantized model can retain 99%+ of the original model's capabilities while being much more efficient.

A concrete example: a 7B parameter LLM that requires 28GB in FP32 format can be reduced to around 7GB with INT8 quantization, making it possible to run on consumer-grade GPUs or even some high-end CPUs.





