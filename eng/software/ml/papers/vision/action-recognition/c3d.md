Learning Spatiotemporal Features with 3D Convolutional Networks (Tran et al., 2015)

https://arxiv.org/pdf/1412.0767.pdf

## Summary
- Introduced C3D
- 3D convolutions can naturally capture temporal features unlike their 2D counterparts
- in 2D CNN architecutures that incorporate multiple frames, most temporal information is lost after the first 2d conv layer?
## C3D
![](../../../images/Pasted%20image%206093976.png)
- Note that the conv kernels are 3x3x3 and the pooling kernels are 2x2x2
### 3D Operations
![](../../../images/Pasted%20image%206143288.png)
- with 3D convs, we get an output **volume**, preserving the temporal information
- same thing applies for 3D pooling
