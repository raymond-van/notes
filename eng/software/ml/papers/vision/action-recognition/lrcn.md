Long-term Recurrent Convolutional Networks for Visual Recognition and Description (Donahue et al., 2014)

https://arxiv.org/pdf/1411.4389.pdf

## Summary
![](../../../images/Pasted%20image%203161743.png)
- CNN weights are shared
- LSTM weights are shared
- Three experiments: output video action recognition, video description or image captioning, as seen below
![](../../../images/Pasted%20image%201633068.png)
- for video classification
	- they take a late-fusion approach to merging the per-time step predictions **y** into a single prediction y for the full sequence
	- Each frame produces a single label prediction, then for the entire video clip, we average the label probabilities – the outputs of the network’s softmax layer – across all frames and choose the most probable label