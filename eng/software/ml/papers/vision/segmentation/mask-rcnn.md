Mask R-CNN (He et al., 2017)

https://arxiv.org/pdf/1703.06870.pdf 

## Summary
- detects objects in an image while simultaneously generating a high-quality segmentation mask for each instance
- extends Faster R-CNN by adding a branch for predicting segmentation masks on each Region of Interest (RoI), in parallel with the existing branch for classification and bounding box regression (Figure 1)
	- the mask branch is simply a small FCN applied to each RoI, predicting a segmentation mask in a pixel-to-pixel manner
![](../../../images/Pasted%20image%2020221219175913.png)
- decouple mask and class prediction and rely on RoI classification branch to predict the category
	- unlike FCNs usually perform per-pixel multi-class categorization, which couples segmentation and classification


## Mask R-CNN
- Faster R-CNN has two outputs for each candidate object, a class label and a bounding-box offset; to this we add a third branch that outputs the object mask
- Thus, there is a multi-task loss for each branch:
	- Loss = L_classification + L_box + L_mask
- Masks are extracted via FCN (fully convolutional + deconvs/upsampling for dense pixel-to-pixel correspondence) 
	- requires RoIAlign
 
![](../../../images/Pasted%20image%2020221219185816.png)
	
## RoIPool vs. RoIAlign
- RoIPool is a standard operation for extracting a small feature map (e.g., 7×7) from each RoI
	- RoIPool first quantizes a floating-number RoI to the discrete granularity of the feature map, this quantized RoI is then subdivided into spatial bins which are themselves quantized, and finally feature values covered by each bin are aggregated (usually by max pooling)
	- Quantization: turn continuous values into discrete values
	- refer to fast-rcnn notes for more details
- Problem with RoIPool is that quantization introduces misalignment between the RoI and the extracted feature map
	- this doesn't have an effect on classification but it does have an effect on creating pixel accurate segmentation masks
- RoIAlign improves object detection and segmentation by aligning the output feature maps of the CNN with the input RoI in the image
	- and does no quantization
	- uses bilinear interpolation to compute the exact values of the input features at four regularly sampled locations in each RoI bin, and aggregate the result (using max or average)
	- Bilinear interpolation:
		- 
- RoIAlign works by dividing the input RoI into a grid of smaller regions and sampling the feature map at the center point of each grid cell
- allows for more accurate localization of objects in the image, as the feature map is not averaged or pooled over the entire RoI as it is in traditional object detection methods

![](../../../images/Pasted%20image%2020221219185331.png)