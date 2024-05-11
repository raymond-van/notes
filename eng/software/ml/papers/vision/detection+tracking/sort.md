SIMPLE ONLINE AND REALTIME TRACKING (Bewley et al., 2016)

https://arxiv.org/pdf/1602.00763.pdf

## Summary
- does online tracking via detection
	- online tracking is where only detections from the previous and the current frame are presented to the tracker 
- MOT can be viewed as a data association problem where the aim is to associate detections across frames in a video sequence
	- use motion and appearance to aid w/ association 
- does not prioritize issues regarding short-term and long-term occlusion + re-identification, focus is on simplicity + efficiency

## SORT
- Unlike other slow trackers, SORT uses CNN based object detectors (Faster R-CNN)
- Kalman filter used to handle motion prediction of a target object from frame-to-frame
- Hungarian method used to handle data association via an IOU association metric from frame-to-frame 
### Motion Model
- used to propogate a target’s identity into the next frame
- approximate the frame-to-frame displacements of each object with a linear constant velocity model (assume object is moving w/ constant velocity) which is independent of other objects and camera motion
- state of each target is modelled as: 
	- ![](../../../images/Pasted%20image%2020221220172732.png)
	 - where dots represent velocities
	- Kalman filter takes in the bounding box coordinates as direct observations of the object state and velocities are estimated
### Data Association
- In assigning detections to existing targets, each target’s bounding box geometry is estimated by predicting its new location in the current frame 
- assignment cost matrix is computed as the intersection-over-union (IOU) distance between each detection and all predicted bounding boxes from the existing targets
	- assignment is solved using Hungarian method
- a minimum IOU is imposed to reject assignments where the detection to target overlap is less than IOU_min
- fuses the predicted boxes with the detection boxes to enhance the detection results?
### Creation and Deletion of Track Identities
- When objects enter and leave the image, unique identities need to be created or destroyed accordingly 
- For creating trackers, any detection with an overlap less than IOU_min (with other boundary boxes) signifies the existence of an untracked object
	- the new tracker undergoes a probationary period where the target needs to be associated with detections to accumulate enough evidence in order to prevent tracking of false positives
- Tracks are terminated if they are not detected for T_lost frames (set to 1 as object re-identification is beyond the scope of this paper)
	- also early deletion of lost targets aids efficiency
	- if an object reappears, it gets a new identity

## Kalman Filter
- an algorithm that uses a series of measurements observed over time, including statistical noise and other inaccuracies, and produces estimates of unknown variables that tend to be more accurate than those based on a single measurement alone, by estimating a joint probability distribution over the variables for each timeframe
- est_t = est_t-1 + KG(data - est_t-1)
	- where KG = Err_est / (Err_est + err_data),       0 <= KG <= 1
		- KG close to 1 means measured data is accurate, kalman estimates are bad
		- KG close to 0 means kalman estimates are good, measured data is bad, thus make new estimate similar to old estimate
![](../../../images/Pasted%20image%2020221220190925.png)

## Hungarian method
- https://www.youtube.com/watch?v=dQDZNHwuuOY
- optimization algorithm that solves the problem of assignment/allocation (assignment cost matrix) to maximize or minimize an objective in O(n^3)