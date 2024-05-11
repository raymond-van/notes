Large-scale Video Classification with Convolutional Neural Networks (Karpathy et al., 2013)

https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/42455.pdf

## Summary
- First large-scale application of CNN to videos on a frame-by-frame basis.
- Explores multi-resolution CNN architecture to speed up performance
	- two separate streams:
		1. Low-res context stream
		2. High-res fovea stream
- Explores different CNN architectures to capture the temporal information that exist in videos
	- Early fusion:
		- first conv layer is modified to take 10 frames as input (frames stacked on top of each other)
	- Late fusion:
		- two separate networks (same parameters) that process a single frame 15 frames apart and get fused later in hopes that the FC layer can capture global motion characteristics by comparing outputs of each network
	- Slow fusion:
		- more sophisticated mixture of early + late fusion
		- each column in early conv layers gets 4 frames w/ overlapping for a total of 10 frames
		- higher conv layers slowly get progressively more information with the upmost conv layers having information from all 10 frames
		
![](../../../images/Pasted%20image%202009232.png)

## Results
- only modest improvements over single-frame cnn 
![](../../../images/Pasted%20image%207701756.png)