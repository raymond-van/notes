Temporal Segment Networks for Action Recognition in Videos (Wang et al., 2017)

https://arxiv.org/pdf/1705.02953.pdf

## Summary
- aims to model long-range temporal structures with a new segment-based sampling and aggregation module
- Identified three problems with applying typical conv nets used in image classification to video:
	1. current conv nets can't capture long range temporal information, only 10 frames
	2. current conv nets can only handle trimmed videos 
	3. video action recognition datasets are relatively small and optical flow extraction is a computational bottleneck for large datasets
- Using RGB and RGB difference as input to TSN, authors were able to build the best-ever (at the time) real-time action recognition system
- TSN framework can be plugged into any existing CNN action recognition model
	- Note the difference between Temporal Segment Network (end-to-end trainable neural net) vs. TSN framework (temporal segment sampling + hierarchical aggregation for untrimmed videos)
### Paper motivation
1. how to effectively learn video representation that captures long range temporal structure; 
2. how to exploit these learned ConvNet models for the more realistic setting of untrimmed videos; 
3. how to efficiently learn the ConvNet models given limited training samples and apply them on large scale data
### Long range temporal structure
- based on observation that consecutive frames are highly redundant, where a sparse and global temporal sampling strategy would be more favorable and efficient in this case
	- although the frames are densely recorded in the videos, the content changes relatively slowly
1. video is divided into a fixed number of segments
2. 1 snippet is randomly sampled for each segment
3. a segmental consensus function is applied to aggregate information from all sampled snippets 
	- e.g. max pooling, average pooling, weighted average
- computational cost is independent of video duration
![](../../../images/Pasted%20image%203193613.png)
### Handling untrimmed videos
- use a hierarchical aggregating strategy named Multi-scale Temporal Window Integration (M-TWI) to yield a prediction result for untrimmed videos
1. again, divide video into short windows of fixed duration
2. perform action recognition for each window independently by max pooling over snippet-level recognition scores inside this window
3. employ top-K pooling or attention weighting to aggregate the predictions from these windows to produce the video-level recognition results
- capable of implicitly selecting intervals with discriminative action instances while suppressing the influence of noisy background
- Refer to Section 4.2 Action Recognition in Untrimmed Videos
### Strategies to overcome limited training data
1. propose a cross-modality initialization strategy to transfer the learned representations from RGB modality to other modalities like optical flow and RGB difference
2. develop a principled method to perform Batch Normalization (BN) in a fine-tuning scenario, denoted as partial BN, where only the mean and variance of first BN layer are updated adaptively to handle domain shift

![](../../../images/Pasted%20image%203195148.png)

##### RGB Differences
- experimented as input to CNN
- alternative to expensive optical flow computation
- hypothesize that the difference between stacked RGB frames could be used to estimate motion, like optical flow