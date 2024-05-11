YOLACT Real-time Instance Segmentation (Bolya et al., 2019)

https://arxiv.org/pdf/1904.02689.pdf

## Summary
- achieves real-time instance segmentation which is much harder than real-time object detectors like YOLO or SSD
- SOTA instance segmentations (e.g. mask rcnn) achieve their performance with a sequential approach which is hard to accelerate
	- they first do **feature localization** which means they “repool” features in some bounding box region (e.g., via RoIpool/align) and then feed these localized features to their mask predictor
	- FCIS does the above steps in parallel but requires a lot of post-processing, thus not real-time
	- YOLACT does not do any feature localization (aka RoIPool/Align)
- breaks instance segmentation into two parallel sub-tasks:
	1. generating a set of prototype masks
	2. predicting per-instance mask coefficients
- Advantages:
	- Very fast
		- 30 fps with ResNet-101 backbone, mask branch only takes 5ms
	- High quality masks
		- since the masks use the full extent of the image space without any loss of quality from repooling, our masks for large objects are significantly higher quality than those of other methods
	- General
		- the idea of generating prototypes and mask coefficients could be added to almost any modern object detector
- sacrifices performance for speed

![](../../../images/Pasted%20image%2020221219205818.png)

![](../../../images/Pasted%20image%2020221219211526.png)
- Figure 2: in prototype masks, we can see that the first 3 masks highlight the person and the last mask highlights the racket, then we take a linear combination of these masks to do instance segmentation

## YOLACT
- adds a mask branch to an existing one-stage object detection model in the same vein as Mask R-CNN does to Faster R-CNN but without an explicit feature localization step (e.g., feature repooling)
### Prototype Masks
- Prototypes are a dictionary/vocabulary (set of features) that can be used to factor an image into different components (can be thought of as the visualization of convolutional feature maps)
	- YOLACT generates prototypes specific to an image to assemble masks for instance segmentation via the Protonet (FCN) (Fig 3)
- YOLACT learns how to localize instances on its own via different activations in its prototypes
- use an FCN (Res-net convolutions) to produce k image-sized "prototype masks" that do not depend on any one instance
	- also uses FPN (pyramid Fig 3)
- attached to a backbone feature layer (Fig 2) eg. resnet
- no prototype loss, loss comes from final mask after assembly
![](../../../images/Pasted%20image%2020221219213246.png)
### Mask Coefficients
- adds an extra head to the object detection branch to predict a vector of k “mask coefficients” (1 coefficient for each prototype) for each anchor that encode an instance’s representation in the prototype space
	- in total, all 3 heads produce 4 + c + k coeffients per anchor 
	- 4 -> bounding box coords, c -> classes, k -> prototypes
- use tanh as nonlinearity for mask coefficients to allow us to subtract a prototypes from mask
![](../../../images/Pasted%20image%2020221219213301.png)
### Assembly
- for each instance that survives NMS, we construct a mask for that instance by linearly combining the work of these two branches
- assembly step is very lightweight (only a linear combination) and can be implemented as one GPU-accelerated mat-mul compsed with sigmoid
### Reasoning
- FC layers (Prediction head), which are good at producing semantic vectors, produce the mask coefficients
- Conv layers, which are good at producing spatially coherent masks, produce the prototype masks
