Fully Convolutional Networks for Semantic Segmentation (Long et al., 2015)

https://arxiv.org/pdf/1411.4038.pdf

## Summary
- adapt ImageNet models (e.g. AlexNet, VGG, LeNet) into fully convolutional nets and finetune them for the semantic segmentation task
- define a novel architecture that combines semantic information from a deep, coarse layer with appearance information from a shallow, fine layer to produce accurate and detailed segmentations 
![](../../../images/Pasted%20image%2020221219130152.png)
- Note: no FC layers in Figure 1, they are turned into convolution layers
![](../../../images/Pasted%20image%2020221219161653.png)
- semantic segmentation faces an inherent tension between semantics and location: global information resolves what while local information resolves where


### FCN
- can take arbitrary sized inputs because we are getting rid of the FC layers and replacing them with convolutional layers (fully connected layers require a fixed dimension as input)
- at this point the output is a coarse, downsampled (as a result of pooling) convolutional heatmap (figure 2)
	- to turn it into pixel-dense output, we perform deconvolutions to upsample from the coarse heatmaps
- per-pixel multinomial logistic loss  and validate with the standard metric of mean pixel intersection over union, with the mean taken over all classes, including background
- define a novel “skip” architecture to combine deep, coarse, semantic information and shallow, fine, appearance information (Figure 3)

![](../../../images/Pasted%20image%2020221219154147.png)
- Figure 3. Our DAG nets learn to combine coarse, high layer information with fine, low layer information. Layers are shown as grids that reveal relative spatial coarseness. Only pooling and prediction layers are shown; intermediate convolution layers (including our converted fully connected layers) are omitted. Solid line (FCN-32s): Our single-stream net, described in Section 4.1, upsamples stride 32 predictions back to pixels in a single step. Dashed line (FCN-16s): Combining predictions from both the final layer and the pool4 layer, at stride 16, lets our net predict finer details, while retaining high-level semantic information. Dotted line (FCN-8s): Additional predictions from pool3, at stride 8, provide further precision.

![](../../../images/Pasted%20image%2020221219172313.png)