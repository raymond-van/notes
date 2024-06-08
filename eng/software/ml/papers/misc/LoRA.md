# LoRA
https://arxiv.org/pdf/2106.09685
- Given a weight matrix $W \in^{d \times k}$, LoRA fine-tunes this weight matrix by decomposing the delta of W into two low-dimensional matrix, that is:
$$
W + \Delta W = W + A \cdot B
$$
- where:
	- $A \in \mathbb{R}^{d \times r}$ 
	- $B \in \mathbb{R}^{r \times k}$
	- r is the rank, a hyperparameter that is much smaller than d and k
		- r could be as low as 1 or 2 while d or k can be as large as $1e^4$
		- as r converges to d, LoRA becomes full fine-tuning
# QLoRA
- pre-trained weights along with low rank A and B matrices are quantized to 4 bits
- 4-bit NormalFloat Quantization
	- normally distributed 4-bit data type
- Double Quantization
	- secondary quantization step that quantizes the quantization constants to save 0.37 bits / parameter
- Paged Optimizers
	- uses NVIDIA unified memory to avoid gradient checkpointing memory spikes
	- model parameters are stored in CPU memory and only the necessary pages are moved to the GPU memory when needed for computation