Rich feature hierarchies for accurate object detection and semantic segmentation (Girshick et al., 2013)

https://arxiv.org/pdf/1311.2524.pdf

## Summary
- Answered the question: 
	- To what extent do the CNN classification results on ImageNet generalize to object detection results on the PASCAL VOC Challenge
- Two key insights:
	1. one can apply high-capacity convolutional neural networks to region proposals in order to localize and segment objects
	2. when labeled training data is scarce, supervised pre-training for an auxiliary task, followed by domain-specific fine-tuning, yields a significant performance boost
- Object detection requires localization of many objects in an image
	- Approaches to localization:
		- Frame it as a regression problem
		- Sliding window detector
			- Problem with CNNs for sliding-window localization is that deeper layers have such a large receptive field that you can't really slide the window
	- R-CNN adopts a "recognition using regions" approach
- R-CNN mainly plays as a classifier, and it does not predict object bounds (except for refining by bounding box regression). Its accuracy depends on the performance of the region proposal module
## R-CNN (Region)
![](../../../images/Pasted%20image%202819432.png)
- generates around 2000 category-independent region proposals for the input image, extracts a fixed-length feature vector from each proposal using a CNN, and then classifies each region with category-specific linear SVMs
- use a simple technique (affine image warping) to compute a fixed-size CNN input from each region proposal, regardless of the region’s shape
- is very slow, test-time detection takes 47s per image for VGG16
	- slow because it performs conv net forward pass for each proposal (2000 per image), without sharing computation
- Three modules
	1. Generate category-independent region proposals which gets passed to the detector
		- Proposals are just candidate regions for the object of interest
		- can use any region proposal method, RCNN uses selective search
	2. Large CNN that extracts a 4096-dim feature vector from each region proposal
		- converts the image data in a region to an input that is computable with the CNN via warping then extracts feature vector
	3. Set of class-specific linear SVMs
		- Score each feature vector for every class using an SVM trained for that class
			- SVM separates data for 1 class vs all other data
		- Given all scored regions in an image, apply greedy NMS that rejects a region if it has an IoU overlap with a higher scoring selected region larger than a learned threshold
- used bounding-box regression to improve localization performance

### Bounding-box Regression (Appendix C)
- after scoring each selective search proposal with a class-specific detection SVM, we predict a new bounding box for the detection using a class-specific bounding-box regressor
- optimizes bounding box by using closed-form solution of least-squares regression for box coordinates and ground truth labels
- "Not linear regression at all. What it does is using a neural network to predict continuous variables, and refers to that as regression. The regression that is defined (which is not linear at all), is just a CNN with convolutional layers, and fully connected layers, but in the last fully connected layer, it does not apply sigmoid or softmax, which is what is typically used in classification, as the values correspond to probabilities. Instead, what this CNN outputs are four values (𝑟,𝑐,ℎ,𝑤) where (𝑟,𝑐) specify the values of the position of the left corner and (ℎ,𝑤) the height and width of the window. In order to train this NN, the loss function will penalize when the outputs of the NN are very different from the labelled (𝑟,𝑐,ℎ,𝑤) in the training set." - https://datascience.stackexchange.com/questions/30557/how-does-the-bounding-box-regressor-work-in-fast-r-cnn

## SVM
- objective is to find a hyperplane (decision boundary) in n-dim space that distinctly classify the data points
- objective is to find the hyperplane that has the maximum margin/distance between data points of both classes
- Maximizing the margin distance provides some reinforcement so that future data points can be classified with more confidence
![](../../../images/Pasted%20image%2020221218112950.png)

## NMS
https://towardsdatascience.com/non-maximum-suppression-nms-93ce178e177c
- Region proposals approaches typically use a sliding window and assign a foreground/background score depending on the features in the window
- Neighbourhood windows have similar scores
- Non-maximum Suppression is used to filter the proposals based on a criteria
- solves multiple counting of the same object by removing the box with the lower confidence probability when the IoU between 2 boxes with the same label is above some threshold
![](../../../images/Pasted%20image%2020221218113042.png)
![](../../../images/Pasted%20image%2020221218173549.png)
### Intersection-over-Union (IoU)
- measures amount of overlap between two proposals
- also used as an evaluation metric for object detectors (evaluate diff between a proposal and the correct label)
- High IoU = large overlap, max = 1?
- simply a ratio

![](../../../images/Pasted%20image%2020221218113104.png)

