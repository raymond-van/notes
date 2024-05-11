SIMPLE ONLINE AND REALTIME TRACKING WITH A DEEP ASSOCIATION METRIC (Wojke et al., 2017)

https://arxiv.org/pdf/1703.07402.pdf

## Summary
- DeepSORT tracks objects not only based on the velocity and motion (SORT) of the object but also the appearance of the object
- allows the tracker to track objects through long periods of occlusion and better handle re-identification, SORT struggled w/ both these issues 
- place much of the computational complexity into an offline pre-training stage where we train a CNN on a large-scale person re-identification dataset to learn a deep association metric that combines both motion and appearance information
- uses cosine distance to ...
![](../../../images/Pasted%20image%2020221221141335.png)

### Tracking Handling
- For each track k we count the number of frames since the last successful measurement association ak. This counter is incremented during Kalman filter prediction and reset to 0 when the track has been associated with a measurement. Tracks that exceed a predefined maximum age Amax are considered to have left the scene and are deleted from the track set. New track hypotheses are initiated for each detection that cannot be associated to an existing track. These new tracks are classified as tentative during their first three frames. During this time, we expect a successful measurement association at each time step. Tracks that are not successfully associated to a measurement within their first three frames are deleted.

## Measurement-to-track Association (Re-ID)
- obtain appearance feature vector for all people from CNN
- then use nearest neighbour on target and appearance vectors computed from CNN, calculated based on mahalanobis distance