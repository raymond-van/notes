# PointNet
- Points in a pointset have the following properties
	- Unordered
		- unlike images, point clouds are a set of points in no particular order
		- model needs to be invariant to the N! permutations of the point cloud
	- Interaction among points
		- the points have a distance metric, meaning neighboring points form a meaningful subset
		- model needs to be able to capture local structures from nearby points
	- Invariant under transformations
		- learned representation of point set should be invariant to certain transformations such as rotations and translations (e.g.: a car upside down is still a car)
- Based on above properties, authors came up with three Modules for object detection and segmentation on point clouds:
	- Max pooling
		- symmetric function that aggregates information from all points
	- Local and global information combination structure
	- T-Net / Joint aligntment networks (2)
		- Uses a mini PointNet network to generate an affine transformation matrix to apply to input coordinates so that the point cloud is invariant under transformations
		- canonicalizes the data
# PointNet++
- PointNet lacks the ability to capture local context at different scales
- applies PointNet recursively to learn local features with increasing contextual scales
- "The general idea of PointNet++ is simple. We first partition the set of points into overlapping local regions by the distance metric of the underlying space. Similar to CNNs, we extract local features capturing fine geometric structures from small neighborhoods; such local features are further grouped into larger units and processed to produce higher level features. This process is repeated until we obtain the features of the whole point set."
- Three key layers: Sampling Layer, Grouping Layer, PointNet layer
	- Sampling -> select set of points that define the centroid of local regions
	- Grouping -> group neighboring points around the centroids (ball query)
	- PointNet -> mini-PointNet to encode local region patterns into feature vectors
- Because point sets have non-uniform density, need adaptive PointNet layers to combine features from regions of different scales when density of points changes
	- Multi-scale grouping
		- apply grouping layers with different scales 
		- features at different scales are concatenated to form a multi-scale feature
		- expensive
	- Multi-resolution grouping
		- concatenate two vectors
			- 1 vector is just normal pointnet on a local region
			- 1 vector is point net subregion of the local region
			- idea is to weigh these two vectors depending on how dense the local region is
- For segmentation need to propagate features from subsampled points to original input size
	- does inverse distanced weighted average interpolation and cross-level skip links
# PointRCNN
## Stage 1
1. Pointnet++ backbone generates point-wise feature vectors
2. From those feature vectors do foreground point segmentation
3. For each foreground point, do bin-based 3d box generation
## Stage 2
- Goal: refine generated 3d box location and orientation
1. Enlarge generated boxes by constant factor to get additional context for refinement
2. Transform points belonging to each proposal to canonical coordinate system
	- at this point we have global semantic features from stage-1 and the canonical local spatial points
3. local + extra (distance, intensity, foreground mask) features enter MLP to become same dimension as global features
4. Concatenate local features to global features -> PointNet++
- transforms RoI pooled points of each proposal to canonical coordinates
- combined RoI pooled points with global semantic features of each point learned in stage-1 for box refinement and predictions
# Point A^2 Net
- expands PointRCNN
- Intra-object part locations
	- relative location of each foreground point w.r.t. the object box that it belongs to 
# MSeg 3D
Paper
- https://arxiv.org/pdf/2303.08600.pdf
Supp
- https://openaccess.thecvf.com/content/CVPR2023/supplemental/Li_MSeg3D_Multi-Modal_3D_CVPR_2023_supplemental.pdf
Code
- https://github.com/jialeli1/lidarseg3d
![[Pasted image 20240409100846.png]]
## Lidar
- Sparse 3D U-Net