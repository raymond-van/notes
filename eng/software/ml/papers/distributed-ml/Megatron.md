# Megatron-LM
- introduces simple synchronization primitives for intra-layer model parallism for transformer architectures
- tensor parallelism only applied to self-attention and mlp layers in transformer
- model parallelism
	- memory and compute of a model is distributed across workers
		- note: cost of passing the weights through memory (bandwidth cost) is also improved
	- tensor computation is distributed
- strong scaling
	- how fast training time decreases if we add more GPUs while keeping model size constant
- weak scaling
	- increasing the batch size proportionally by the number of available workers
## f and g 
- f and g are conjugate functions used to either split (across GPUs) or all-reduce a weight matrix
- f is an identity operator in the forward pass and all reduce in the backward pass 
```
class f(torch.autograd.Function): 
	def forward(ctx, x): return x 
	def backward(ctx, gradient): all_reduce(gradient)
```
- g is an all reduce in the forward pass and identity in the backward pass
## MLP Parallelization
- Transformer layer consists of a two-layer MLP
- First MLP layer is split column-wise
- Second MLP layer is split row-wise
## Self-Attention Parallelization
- exploit inherent parallelism in the multihead attention operation such that the GEMM corresponding to each attention head is done locally on one GPU
## Megatron-LM 2
- https://people.eecs.berkeley.edu/~matei/papers/2021/sc_megatron_lm.pdf
- combines data, model and pipeline parallelism
- Pipeline Parallelisim
	- model is sharded layer-wise across devices
	- batches are split into microbatches such that each pipeline can process microbatches asynchronously
		- enables a pipe to move onto the next microbatch as soon as it completes first microbatch
## Megatron-LM 3
- https://arxiv.org/pdf/2205.05198
- Reduces activation recomputation by introducing **Sequence Parallelism and Selective Activation Recomputation**
- Activation memory becomes bottleneck as model becomes larger
- Activation Computation
	- instead of storing intermediate activations / gradients, we re-compute them to save on memory
- tensor parallelism introduced in first paper didn't touch layer norm and dropout
- Layer norm and dropout require little compute but non-trivial amount of activation memory
### Sequence Parallelism
- non-tensor parallel operations are independent along the sequence dimension (layer-norm and dropout)
	- each position in the input sequence can be processed without affecting the results for other positions
- partitions along the sequence dimensions to parallelize layernorm and dropout layers
- LayerNorm
	- once we obtain the activation layer statistics (mean, std dev) the normalization of an activation is independent of other activations in the layer 
- Dropout
	- whether an element is dropped-out or not is independent of the other activations