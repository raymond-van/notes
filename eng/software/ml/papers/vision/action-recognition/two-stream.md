Two-Stream Convolutional Networks for Action Recognition in Videos (Simonyan et al., 2014)

https://arxiv.org/pdf/1406.2199.pdf

## Summary
- Introduced two-stream CNN networks for action recognition (both networks are CNNs, just different inputs)
	- Spatial network captures information in single frame
	- Temporal network captures motion/temporal information by using optical flow as input
	- Networks are combined via late fusion (refer to deepvideo notes)
- Architecture is based on Two-streams hypothesis: 
	- the human visual cortex contains two pathways: the ventral stream (which performs object recognition) and the dorsal stream (which recognises motion)
- Decoupling the spatial and temporal nets ?allows to exploit the available images by pretraining spatial nets on the ImageNet challenge dataset, and explicitly handle motion information with optical flow algorithms for temporal nets

![](../../../images/Pasted%20image%202180445.png)


### Temporal Stream inputs
- Optical flow stacking
- Trajectory flow stacking
![](../../../images/Pasted%20image%202185927.png)
- where d_t(u,v) is the displacement vector at the point (u, v) in frame t