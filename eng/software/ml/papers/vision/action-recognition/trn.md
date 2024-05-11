Temporal Relational Reasoning in Videos (Zhou et al., 2018)

https://arxiv.org/pdf/1711.08496.pdf

## Summary
- Temporal Relational Reasoning is the ability to link meaningful transformations (in this context, motion) of objects or entities over time
	- e.g.: given two observations of an event, people can easily recognize the temporal relation between two states of the visual world and deduce what has happened between the two frames of a video
	- A single activity can consist of several temporal relations at both short-term and long-term timescales. For example, the activity of sprinting contains the long-term temporal relations of crouching at the starting blocks, running on track, and finishing at the end line, while it also includes the short-term temporal relations of periodic hands and feet movement.
- Current video recognition networks struggle to discover visual common sense knowledge over time beyond the appearance of objects in the frames and the optical flow
- fast enough to do real-time processing
### TRN
- is designed to learn and reason about temporal dependencies of video frames at multiple time scales
- is a module that can be plugged into any existing video recognition CNN
- sparsely samples individual frames and then learns their causal relations, which is much more efficient than sampling dense frames and convolving them
	- existing CNN architectures use dense sequence of frames as input, however there is a lot of redundancy in consecutive frames -> makes more sense to sparsely sample
- can efficiently capture temporal relations at multiple time scales and outperform dense frame-based networks using only sparsely sampled video frames
![](../../../images/Pasted%20image%208829068.png)

Two-frame Temporal Relation
![](../../../images/Pasted%20image%201947041.png)

Multi-scale temporal relation

![](../../../images/Pasted%20image%201247443.png)