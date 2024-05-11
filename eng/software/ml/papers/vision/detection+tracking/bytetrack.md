ByteTrack: Multi-Object Tracking by Associating Every Detection Box (Zhang et al., 2022)

https://arxiv.org/pdf/2110.06864.pdf

## Summary
- typical MOT methods associate objects with identities by associating objects with detection boxes that are higher than a threshold and simply throw away objects with low detection scores (which include occluded objects) 
	- they do this to handle true positive / false positive tradeoff
- ByteTrack associates almost every detection box instead of only high score ones 
	- for the low score detection boxes, we utilize their similarities with tracklets to recover true objects and filter out the background detections

![](../../../images/Pasted%20image%2020221221163841.png)
- Figure 2: if we take every detection box into consideration, more false positives will be introduced immediately, e.g., the most right box in frame t3
	- use similarities with tracklets to distinguish between objects and background
	 - As shown in Figure 2 (c), two low score detection boxes are matched to the tracklets by the motion model’s predicted boxes, and thus the objects are correctly recovered. At the same time, the background box is removed since it has no matched tracklet
## ByteTrack
- calls detection boxes BYTES as each detection box is a basic unit of the tracklet, as byte in computer program, and our tracking method values every detailed detection box
- First match the high score detection boxes to the tracklets based on motion similarity or appearance similarity (Figure 2b)
	- use Kalman filter to predict the location of tracklets in new frame
	- similarity can be computed by the IoU or Re-ID feature distance of the predicted box and the detection box
- Second match between the unmatched tracklets, i.e. the tracklet in red box, and the low score detection boxes using the same motion similarity (Figure 2c)
	- Moreover, the occluded person with low detection scores is matched correctly to the previous tracklet and the background (in the right part of the image) is removed