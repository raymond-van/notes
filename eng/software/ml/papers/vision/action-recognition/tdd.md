Action Recognition with Trajectory-Pooled Deep-Convolutional Descriptors (Wang et al., 2015)

https://arxiv.org/pdf/1505.04868.pdf

## Summary
- previous cnn architectures for action recognition (deep-video, two-stream) lacked consideration of intrinsic temporal characteristics in video and relied on large datasets
	- this paper incorporates temporal info into cnns by using trajectory-constrained sampling and pooling (improved dense trajectories), giving us new descriptors (TDD)
	- use Fisher vectors to aggregate local TDDs over the whole video into a super global vector
	- global vector is passed into a linear SVM for classification
- flaw of local hand-crafted features is that they lack semantics and discriminative capacity (img classification...) -> thus use two-stream CNN to learn these features

![](../../../images/Pasted%20image%2013112618.png)
- Note: inputs in conv nets (right) have multiple scales, input to spatial net is still a single frame (multiple res/scale) and input to temporal net is still a 3d volume optical flow field at multiple scales

### Architecture 
#### Trajectories
- Trajectory of a pixel is just tracking the location of a pixel through time
- Dense trajectories are computed via "Action Recognition with Improved Trajectories" but only on a single scale
#### Two-stream CNN
- Like two-stream CNN (Simonyan) but instead of classification used as a feature extractor (classification is done in TDD step) to obtain a pyramid of convolutional feature maps at each conv layer
- like typical CNNs, the lower conv layers find edges whereas higher conv layers have a larger receptive field allowing it to capture more complex visual features
#### TDD
- TDD is a kind of local trajectory-aligned descriptor computed in a 3D volume around the trajectory
- From the spatial and temporal nets, TDD captures the appearance and motion information of this 3D volume, respectively.
- TDD is composed of two steps: feature map normalization and trajectory pooling
#### Feature map normalization 
- Spatiotemporal Normalization (divide by max value in a channel)
	- ensures that each convolutional feature channel ranges in the same interval, and thus contributes equally to final TDD recognition performance
- Channel Normalization (divide by max value in a pixel location across all channels)
	- make sure that the feature value of each pixel range in the same interval, and let each pixel make the equal contribution in the final representation
#### Trajectory pooling
- Sum-pooling of the normalized feature maps over the 3D volume centered at the trajectory as follows
![](../../../images/Pasted%20image%203154652.png)

##### Warped optical flow field
- Removes the camera motion from optical flow field? and keeps the optical flow for the motion we care about 