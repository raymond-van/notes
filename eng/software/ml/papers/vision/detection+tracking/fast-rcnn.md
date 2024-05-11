Fast R-CNN (Girshick, 2015)

https://arxiv.org/pdf/1504.08083.pdf

## Summary
- employs several improvements over R-CNN
	- trains VGG16 network 9x faster than R-CNN
	- 213x faster at test-time with better performance
- object detection is hard because it requires the accurate localization of objects which creates two primary challenges:
	1. numerous candidate proposals must be processed
	2. these candidates provide only rough localization that must be refined to achieve precise localization
- introduced a single-stage cnn that learns to classify object proposals and refine their spatial locations (whereas r-cnn had 3 stages)
- r-cnn is slow because it performs a ConvNet forward pass for each object proposal (~2000), without sharing computation
- SPPnet method computes a convolutional feature map for the entire input image and then classifies each object proposal using a feature vector extracted from the shared feature map
	- but is also a multi-stage pipeline
	
## Fast R-CNN
![](../../../images/Pasted%20image%2020221218120344.png)
- takes as input an entire image and a set of object proposals
	- thus you still need to do some pre object-proposal computation
- The network first processes the whole image with several convolutional (conv) and max pooling layers to produce a conv feature map. Then, for each object proposal a region of interest (RoI) pooling layer extracts a fixed-length feature vector from the feature map. Each feature vector is fed into a sequence of fully connected (fc) layers that finally branch into two sibling output layers: one that produces softmax probability estimates over K object classes plus a catch-all “background” class and another layer that outputs four real-valued numbers for each of the K object classes. Each set of 4 values encodes refined bounding-box positions for one of the K classes
### Region of Interest (RoI)
- a rectangular window (h x w) in a conv feature map (corresponds to a proposal)
- RoI Pooling:
	- divide the h x w window into an H x W grid where each sub window has size h/H x w/W  and then max-pool each subwindow 
	- RoI pooling layer uses max pooling to convert the features inside any valid region of interest into a small feature map with a fixed spatial extent of H × W (e.g., 7 × 7), where H and W are layer hyper-parameters that are independent of any particular RoI
- RoIs from the same image share computation and memory in the forward and backward passes
### Multi-task Loss
![](../../../images/Pasted%20image%2020221218124601.png)
