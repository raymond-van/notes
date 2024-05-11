Attention Is All You Need (Google Brain, 2017)

https://arxiv.org/pdf/1706.03762.pdf

#### Helpful links
- http://nlp.seas.harvard.edu/annotated-transformer/
- http://jalammar.github.io/illustrated-transformer/
- Masking: https://medium.com/analytics-vidhya/masking-in-transformers-self-attention-mechanism-bad3c9ec235c#:~:text=Masking%20is%20needed%20to%20prevent,present%20in%20the%20encoder%20side.
- https://e2eml.school/transformers.html

## Summary
- Attention mechanisms allowing modeling of dependencies without regard to their distance in the input or output sequences
- Gets rid of recurrent layers and relies entirely on attention mechanisms to draw global dependencies between input and output
- Self-attention
	- an attention mechanism relating different positions of a single sequence in order to compute a representation of the sequence
- computationally efficient and scalable, making it possible to train models with >100B parameters

## Transformer
- follows typical seq2seq architecture with encoder-decoder structure
![](../../images/Pasted%20image%2020221222160935.png)
### Encoder
- stack of N = 6 identical layers, with each layer having 2 sub-layers
- Sub-layer 1: Multi-head attention, residual connection, then layer-norm (LayerNorm(x + sublayer(x)))
	- attention layer is a self-attention layer, meaning all of the keys, values and queries come from the same place, in this case, the output of the previous layer in the encoder
- Sub-layer 2: Feed-forward, residual connection, then layer-norm (LayerNorm(x + sublayer(x)))
### Decoder
- stack of N = 6 identical layers, with each layer having 3 sub-layers
- same two sub-layers as above but with an additional sub-layer that performs multi-head attention over the output of the encoder stack
- Masked attention: modify the self-attention sub-layer in the decoder stack to prevent positions from attending to future positions 
	- this masking, combined with fact that the output embeddings are offset by one position, ensures that the predictions for position i can depend only on the known outputs at positions less than i
### Attention
- attention function can be described as mapping a query and a set of key-value pairs to an output, where the query, keys, values, and output are all vectors
	- queries come from the previous decoder layer, and the memory keys and values come from the output of the encoder
		- this allows every position in the decoder to attend over all positions in the input sequence
- output is computed as a weighted sum of the values, where the weight assigned to each value is computed by a compatibility function of the query with the corresponding key 
- #### Scaled Dot-Product Attention
![](../../images/Pasted%20image%2020221222163444.png)
- scaled by 1/sqrt(d_k) where d_k is the dimension of the key vector to prevent dot-products from growing too large, which in turn effects the gradients of the softmax
- Have key-value pairs corresponding to position/idx in input sequence and the token/feature/value at that position
- Dot product between query and key denotes which positions in the input sequence are necessary to answe the query
	- orthogonal vector-pairs between query and key/position will give 0 whereas positions in input sequence that can be helpful in answering the query will be larger
- Then we scale and put in softmax to give a distribution of the positions we neeed
- Finally, extract those positions via a mat-mul with the values
![](../../images/Pasted%20image%2020221222162850.png)
#### Multi-Head Attention
- Instead of performing a single attention function with d_model-dimensional keys, values and queries, we found it beneficial to linearly project the queries, keys and values h times with different, learned linear projections to d_k, d_k and d_v dimensions, respectively. On each of these projected versions of queries, keys and values we then perform the attention function in parallel, yielding dv-dimensional output values. These are concatenated and once again projected, resulting in the final values
	- Note: by linear projections they just mean a mat-mul via learned weight matrices
- Multi-head attention allows the model to jointly attend to information from different representation subspaces at different positions
![](../../images/Pasted%20image%2020221222164929.png)
### Embeddings and Softmax
- use learned embeddings to convert the input tokens and output tokens to vectors of dimension dmodel
- use the usual learned linear transformation and softmax function to convert the decoder output to predicted next-token probabilities
### Positional Encoding
- need to add positional encodings to the input embeddings at the bottoms of the encoder and decoder stacks to give our model some information about that relative/absolute position of the tokens in sequence
- implemented via different sine and cosine frequencies
## Why use attention
- Learning long-range dependencies is a key challenge in many seq2seq tasks
	- attention solves the issue of path length between long-range dependencies in the network
	- forward and backward signals have to make long traversals in RNN
- a self-attention layer connects all positions with a constant number of sequentially executed operations, whereas a recurrent layer requires O(n) sequential operations