TSM: Temporal Shift Module for Efficient Video Understanding (Lin et al., 2018)

https://arxiv.org/pdf/1811.08383.pdf

## Summary
- as performant as 3D CNNs, as efficient as 2D CNNs
- module can be inserted into any existing 2D CNN architecture at 0 extra cost
- can do real-time low-latency online video action recognition
## TSM
- take feature map (5D tensor - H,W,C,T,N=batchsz) that has been outputted from a 2D CNN and perform a **partial** shift (only some channels) along temporal dimension
- in offline setting:
	- at each time step, a channel will contain information from the previous frame, the current frame and the next frame
- in online setting:
	- at each time step, a channel will contain only information from the previous frame and the current frame as future frames cannot be shifted to the present
 
![](../../../images/Pasted%20image%204337229.png)
- Fig 1: each frame/time-step is color coded, thus in (b), each time-step will have 3 colors representing information from 3 frames
![](../../../images/Pasted%20image%205228432.png)
- Fig 3: insert TSM inside residual branch (right) rather than outside (left) so that the activation of the current frame is preserved, which does not harm the spatial feature learning capability of the 2D CNN backbone
	- X gets passed cleanly across to preserve spatial feature learning
![](../../../images/Pasted%20image%205525648.png)
- Fig 4: a partial temporal shift is done in each residual block