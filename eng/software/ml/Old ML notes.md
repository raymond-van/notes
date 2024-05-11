## Architectures
### Conv
- https://victorzhou.com/blog/intro-to-cnns-part-2/#1-setting-the-stage
- captures local context of objects in images via filters
- the output of convolving an image with a (Sobel) filter is an image that emphasises its edges
	- Sobel filters are just special filters, e.g. horizontal/vertical sobel that can detect edges in an image
- spatial invariance, i.e. a dog is still a dog whether its in 1 corner or another part of the image
- Pooling
	- Neighboring pixels in images tend to have similar values, so conv layers will typically also produce similar values for neighboring pixels in outputs. As a result, **much of the information contained in a conv layer’s output is redundant**.
	- e.g.:  we use an edge-detecting filter and find a strong edge at a certain location, chances are that we’ll also find relatively strong edges at locations 1 pixel shifted from the original one. However, **these are all the same edge!** We’re not finding anything new.
### RNN
- https://victorzhou.com/blog/intro-to-rnns/#1-the-why
- http://karpathy.github.io/2015/05/21/rnn-effectiveness/
- repeating module consists of just a tanh layer (colah)
- hidden state stores information about past allowing it to remember things about its past
- can handle variable length input and output
- problem with RNNs is that the amount of context stored in the hidden state is relatively small, that is, information that has been stored a long time ago may not be remembered deep in the future, resulting in poor predictions --> LSTMs (colah)
- backpropagation through time
### LSTM
- capable of learning long-term dependencies compared to RNNs
- better at handling exploding/vanishing gradients
- Repeating module:
	- four layers
	- Top horizontal line is the cell state
	- Consists of:
		- Forget gate (sigmoid) dictates what info in cell state gets dropped
		- Input gate (sigmoid) dictates which info in cell state need to get updated
		- 1st tanh gate creates new cell state values which gets multiplied by input gate and then added to the cell state to get new cell state
		- Output gate (sigmoid) dictates which part of the cell state will get outputted to h_t, then we squash values of cell state to [-1, 1] via tanh, then multiply it by the output gate so that we only output parts of the cell state that we want to
### VAE
### Attention

## Non-linearities
### Sigmoid
f(x) = 1 / 1+e^-x
df(x)/dx = e^-x / (1 + e^-x)^2 =  f(x)(1 - f(x))

### Logistic function
- https://peterroelants.github.io/posts/cross-entropy-logistic/
- used for the classification of two target classes
### Softmax
- https://victorzhou.com/blog/softmax/
- extension of the logistic function
	- output is a categorical probability distribution
- turn real values into a probability distribution
- denominator acts as a normalizer to make sure that the sum of output of softmax is 1
### ReLU
### tanh
- range of [-1,1] as opposed to sigmoid which has range [0, 1]
## Loss Functions
### MSE
### Cross entropy loss (Negative log-likelihood)
https://peterroelants.github.io/posts/cross-entropy-softmax/
- maximize the likelihood function (P(x | theta)) is equivalent to minimizing the NLL
- L = -ln(p_c) where p_c is the predicted probability of the true class
	- Note: 0 <= p_c <= 1 and p_c = 1 means 0 loss
- often paired with softmax (bc softmax outputs probabilities)

## Optimization Functions
### SGD
### Adam
### RMSprop

## Misc.
### Reparameterization trick

### LogSumExp trick 
- avoids underflow/overflow for softmax (cause alot of exp)? and others too?

### Weight Init.
#### Xavier
#### He

### Normalization
### Exploding / Vanishing Gradients
- problem in RNNs -> clip / use LSTMs

### ML Math
- Matrix calculus (https://atmos.washington.edu/~dennis/MatrixCalculus.pdf)
	- Cookbook (https://www.math.uwaterloo.ca/~hwolkowi/matrixcookbook.pdf)