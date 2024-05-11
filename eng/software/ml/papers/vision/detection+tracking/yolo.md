## YOLO family (1-stage) vs. R-CNN family (2-stage)
- https://www.reddit.com/r/computervision/comments/l5ybwv/what_is_the_actual_difference_between_yolo_and/
- https://machinethink.net/blog/object-detection/

## v1
You Only Look Once: Unified, Real-Time Object Detection (Redmon et al., 2015)

https://arxiv.org/pdf/1506.02640.pdf
![](../../../images/Pasted%20image%2020221218191411.png)
### Summary
- Doing test-time detection for a single image with models in the R-CNN family have to do a forward pass for each region proposal whereas in YOLO you only need to do a single forward pass (look once)
- single end-to-end trainable network that frames object detection as a regression problem to spatially separated bounding boxes and associated class probabilities
- 
### YOLO
![](../../../images/Pasted%20image%2020221219104720.png)
- Divides an input image into an S x S grid, each grid cell will be responsible for predicting B bounding boxes, confidence scores for those boxes and C conditional class probabilities
	- Bounding box:
		- consists of predicting (x, y, h, w) coordinates of box
		- perform regression over the predicted box and any ground truth box in the cell
	- Confidence score:
		- reflect how confident the model is that the box contains an object and also how accurate it thinks the box is that it predicts
		- formally defined as ![](../../../images/Pasted%20image%2020221219104858.png)
		- If no object exists in that cell, the confidence scores should be zero. Otherwise we want the confidence score to equal the intersection over union (IOU) between the predicted box and the ground truth
	- Conditional Class Probability:
		- Pr(Class_i | Object), the condition is on whether the grid cell contains any object
		- only predict one set of class probabilities per grid cell, regardless of number of bounding boxes B we predict
	- ![](../../../images/Pasted%20image%2020221219105613.png)
- initial convolutional layers of the network extract features from the image while the fully connected layers predict the output probabilities and coordinates 
![](../../../images/Pasted%20image%2020221219110209.png)
- Fast-YOLO has 9 layers as opposed to 24 layers

## v2 (+ yolo9000)
YOLO9000: Better, Faster, Stronger (Redmon, 2016)
https://arxiv.org/pdf/1612.08242.pdf
### Summary
- YOLO9000 can detect over 9000 objects  
	- proposed a method to jointly train on object detection and classification. Using this method we train YOLO9000 simultaneously on the COCO detection dataset and the ImageNet classification dataset. Our joint training allows YOLO9000 to predict detections for object classes that don’t have labelled detection data
- YOLOv2 improvements:
	- added batch normalization to all conv layers, allows us to remove dropout
	- instead of using FC layers to predict box coordinates, v2 adopts faster r-cnn approach of using conv layers to predict offsets for anchor boxes
	- multi-scale training 
	- among many others...

## v3
YOLOv3: An Incremental Improvement (Redmon, 2018)

https://arxiv.org/pdf/1804.02767.pdf
- increased # conv layers to 53 
- added objectness score to bounding box predictions
- multi-scale


## Rest of Yolo architectures
- https://blog.roboflow.com/guide-to-yolo-models/