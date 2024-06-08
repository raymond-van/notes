# FlashAttention
- regular self-attention 
	- is quadratic
	- is memory intensive due to storing intermediate results in HBM memory as opposed to on-chip shared memory
![](Pasted%20image%2020240603202749.png)
- Flash attention
	- is IO-aware
		- minimizes HBM memory access
![](Pasted%20image%2020240603202826.png)
- reducing memory overhead and optimizing computation, allows for larger batch sizes and input sequences
## IO-aware Attention
- accounts for reads and writes to HBM (slow) and SRAM (fast) which have contributed to a large portion of runtime
- Goal: avoid reading and writing attention matrix to and from HBM
	- requires 1) computing the softmax reduction without access to the whole input and 2) not storing the large intermediate attention matrix for the backward pass
- Accomplishes goal by:
	- 1) Tiling: restructuring the attention computation to split the input into blocks and make several passes over input blocks, thus incrementally performing the softmax reduction (also known as tiling)
	- 2) Gradient checkpointing: storing the softmax normalization factor from the forward pass to quickly recompute attention on-chip in the backward pass, which is faster than the standard approach of reading the intermediate attention matrix from HBM
- upto 7x faster and linear memory usage
- 9x less HBM accesses
## GPU Properties
- compute speed has outpaced memory / bandwidth speed
- results in memory access being the bottleneck
## Block-sparse Attention
# FlashAttention 2