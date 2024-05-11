AN IMAGE IS WORTH 16X16 WORDS: TRANSFORMERS FOR IMAGE RECOGNITION AT SCALE (Google Brain, 2020)

https://arxiv.org/pdf/2010.11929.pdf

## Summary
- got rid of conv layers and showed that a pure transformer architecture can perform very well on image classification tasks 
	- split an image into patches and provide the sequence of linear embeddings of these patches as an input to a Transformer
	- Image patches are treated the same way as tokens (words) in an NLP application 
- on mid-sized datasets yield modest accuracies of a few percentage points below ResNets of comparable size
	- Transformers lack some of the inductive biases inherent to CNNs, such as translation equivariance and locality, and therefore do not generalize well when trained on insufficient amounts of data
- different story when ViT is trained on large datasets (14M - 300M images) 
	- attains excellent results when pre-trained at sufficient scale and transferred to tasks with fewer datapoints
 - Naive application of self-attention to images would require that each pixel attends to every other pixel
	 - O(n^2^2) complexity, n is spatial dimension of image
## ViT
- just Transformer but applied to patches of the image
- linearly embed/projection = matmul
![](../../../images/Pasted%20image%2020221222183029.png)
- prepend a learnable embedding to the sequence of embedded patches, whose state at the output of the Transformer encoder serves as the image representation y 