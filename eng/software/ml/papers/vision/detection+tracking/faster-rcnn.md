Faster R-CNN: Towards Real-Time Object Detection with Region Proposal Networks (Ren et al., 2015)

https://arxiv.org/pdf/1506.01497.pdf

## Summary
- bottleneck of previous object detection networks has been region proposal
- introduced a Region Proposal Network (RPN) that shares full-image convolutional features with the detection network, thus enabling nearly cost-free region proposals
	- compute proposals using the same CNN that does detection (thus, nearly cost-free) as opposed to previous expensive methods e.g. selective search
	- module that can be plugged into a detection network
- An RPN is a fully convolutional network that simultaneously predicts object bounds and objectness scores at each position
	- objectness: refers to how likely is it an object (belonging to one of the classes) vs. background
- The RPN is trained end-to-end to generate high-quality region proposals, which are used by Fast R-CNN for detection
- further merge RPN and Fast R-CNN into a single network by sharing their convolutional features using attention mechanisms (the RPN component tells the unified network where to look)

![](../../../images/Pasted%20image%2020221218133801.png)
## Faster R-CNN
- RPN + Fast R-CNN
## Region-Proposal Network (RPN)
- generates proposals not from the raw image but from the convolutional feature maps
- RPN is a module of conv layers that simultaneously regress region bounds and objectness scores at each location on a regular grid
	- can be trained end-to-end specifically for the task for generating detection proposals
- to introduce RPN to an object-detection network like Fast R-CNN, authors used a training scheme that alternates between fine-tuning for the region proposal task and then fine-tuning for object detection, while keeping the proposals fixed
	- results in convolutional features that shared between both tasks
- To generate region proposals, we slide a small network over the convolutional feature map output by the last shared convolutional layer. This small network takes as input an n × n spatial window of the input convolutional feature map. Each sliding window is mapped to a lower-dimensional feature. This feature is fed into two sibling fullyconnected layers—a box-regression layer (reg) and a box-classification layer (cls) 
	- architecture is naturally implemented with an n×n convolutional layer followed by two sibling 1 × 1 convolutional layers (for reg and cls, respectively)
 
![](../../../images/Pasted%20image%2020221218143642.png)
- At each sliding-window location, we simultaneously predict multiple region proposals, where the number of maximum possible proposals for each location is denoted as k. So the reg layer has 4k outputs encoding the coordinates of k boxes, and the cls layer outputs 2k scores that estimate probability of object or not object for each proposal
- The k proposals are parameterized relative to k reference boxes, which we call anchors. An anchor is centered at the sliding window in question, and is associated with a scale and aspect ratio (Figure 3). By default we use 3 scales and 3 aspect ratios, yielding k = 9 anchors at each sliding position. For a convolutional feature map of a size W × H (typically ∼2,400), there are W Hk anchors in total.
