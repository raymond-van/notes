Quo Vadis, Action Recognition? A New Model and the Kinetics Dataset (Carreira et al., 2017)

https://arxiv.org/pdf/1705.07750.pdf

## Summary
- introduces a new Two-Stream Inflated 3D ConvNet (I3D) that is based on 2D ConvNet inflation: filters and pooling kernels of very deep image classification ConvNets are expanded into 3D
	- previous 3D ConvNets have been very shallow (8 layers) due to high parameter dimensionality and lack of labeled video data
- leverages successful ImageNet architecture designs and their parameters
- introduced new Kinetics Human Action Video Dataset, which is two orders of magnitude larger than previous datasets, HMDB-51 and UCF-101 
	- did pretraining on Kinetics to outperform SOTA on HMDB and UCF

![](../../../images/Pasted%20image%205190876.png)
	
## I3D
- "Two-Stream Inflated 3D ConvNets"
	- can directly learn about temporal patterns from an RGB stream, their performance can still be greatly improved by including an optical-flow stream
- builds upon SOTA image-classifiers by inflating their filters and pooling kernels from 2D to 3D 
	- their pretrained weights provide valuable initialization
- based on Inception V1 
- Inflating: 
	- filters are endowed with an additional  temporal dimension
	- repeat the weights of the 2D filters N times along the time dimension, and rescale them by dividing by N 
	- square N x N filters -> Cubic N x N x N filters
- when adding an additional temporal dimension across a CNN architecture, we need to consider the time receptive field which depends on frame rate and image dimensions
	- if it grows too quickly in time relative to space, it may conflate edges from different objects breaking early feature detection (lose spatial features), while if it grows too slowly, it may not capture scene dynamics (lose temporal features) well
	
![](../../../images/Pasted%20image%203351231.png)
- note there will be another I3D that is trained on optical flow frames