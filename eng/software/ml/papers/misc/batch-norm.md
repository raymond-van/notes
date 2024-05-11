Batch Normalization: Accelerating Deep Network Training b y Reducing Internal Covariate Shift (Ioffe and Szegedy, 2015)

https://arxiv.org/pdf/1502.03167.pdf

Note: can often be replaced w/ layer-norm ?

## Summary
- Internal Covariate Shift:
	- when the distribution of each layer’s inputs changes during training, as the parameters of the previous layers change
	- this is a problem because the layers need to continuously adapt to the new distribution (AKA covariate shift)
		 - "internal" covariate shift refers to the fact that the shift occurs not to the system as a whole but rather to its parts such as a sub-network or layer
		 - handled via domain adaptation?
	- requires lower learning rates, more careful parameter init. and harder to train models w/ saturating nonlinearities
## Batch Norm:
- reduces internal covariate shift
- allows us to use higher learning rates
- reduces the dependence of gradients on the scale of the parameters or of their initial values
- makes it possible to use saturating nonlinearities
- result:  
- match performance of best ImageNet classifiier using only 7% of the training steps
 - based on input whitening (linearly transforming inputs to have 0 mean and unit variance) which results in faster convergence
	  - BatchNorm whitens inputs to each layer and not just at start
- need to ensure that the gradient descent optimization takes into account the fact that the normalization/whitening takes place

... Let x be the output of an arbitrary layer in the network, represented as a vector, and ...
![](Pasted%20image%2020230118190507.png)
## Normalization via mini-batch statistics
![](../../images/Pasted%20image%2020230118191237.png)
- computed over batch dimension..
- Noted that normalizing each input of a layer can change what the layer can represent
	- i.e. normalizing the inputs of a sigmoid could restrict outputs to only the linear portion of the non-linearity
- To restore representation power of the network, need to ensure that the normalization transformation can represent the *identity*
- Does this by introducing a pair of new learnable parameters for each activate x^(k), which shift and scale the normalized value:
![](../../images/Pasted%20image%2020230118193729.png)
- each mini-batch produces estimates of the mean and variance of each activation
- statistics used for normalization can fully participate in the gradient backpropagation
![](../../images/Pasted%20image%2020230118194308.png)
- For inference, use population statistics rather than mini-batch stats for normalization

![](../../images/Pasted%20image%2020230118194816.png)