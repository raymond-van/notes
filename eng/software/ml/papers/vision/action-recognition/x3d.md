X3D: Expanding Architectures for Efficient Video Recognition (Feichtenhofer, 2020)

https://arxiv.org/pdf/2004.04730.pdf

## Summary
- To apply 2D CNNs to video recognition, prior works has been focused on expansion on the temporal axis and other design decisions such as depth (number of layers), width (number of channels), and spatial sizes are typically inherited from 2D image architectures
	- although these methods have been successful in terms of accuracy, these methods are usually sub-optimal if we look at the computation/accuracy trade-off (Image ResNet vs Expanded video ResNet takes 27x more multiply-adds)
- X3D expands 2D CNNs along multiple network axes: temporal, frame rate, spatial resolution (h,w), width, bottle-neck width and depth
- On step-by-step basis, X3D expands along one of the aforementioned axes to obtain desired accuracy to complexity tradeoff
- To expand X3D to a specific target complexity, performs progressive forward expansion followed by backward contraction
	- Progressive Feature Selection:
		- progressive feature selection methods start with either a set of minimum features and aim to find relevant features to improve in a greedy fashion by including (forward selection) a single feature in each step, or start with a full set of features and aim to find irrelevant ones that are excluded by repeatedly deleting the feature that reduces performance the least (backward elimination)
- Produced a sequence of spatiotemporal architectures, covering a wide range of computation/accuracy trade-offs that can be used under different computational budgets
- SOTA performance with 5x less multiply-adds and parameters

![](../../../images/Pasted%20image%205917432.png)

## X3D
- requires to train 6 models, one for each expansion axis, until a desired complexity is reached, e.g. for 5 steps, it requires 30 models to be trained
### Progressive Network Expansion
- "The 2D base architecture is driven by the MobileNet core concept of channel-wise separable convolutions. Our expansion then progressively increases the computation (e.g., by 2×) by expanding only one axis at a time, train and validate the resultant architecture, and select the axis that achieves the best computation/accuracy trade-off. The process is repeated until the architecture reaches a desired computational budget. This can be interpreted as a form of coordinate descent in the hyper-parameter space defined by those axes."