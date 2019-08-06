#### Depth Estimation Conclusion

In 3D computer vision domain, there is one called depth estimation (depth prediction, depth inference), which will predict the depth of the pixel on images. So with this technique, if we have excellent estimation of the pixel depth, we can get dense 2D matrix depth from surroundings. From this aspect, depth estimation of camera capture can be better than LiDAR. LiDAR depth is a kind of sparse beams of depth. Besides, with the help of depth estimation, if the LiDAR is fused with LiDAR, we can use depth completion to get dense and absolute accurate scale 2D dense depth. With the depth information, we can be benefit on tracking, obstacle detection and further path planning. All in all, depth estimation can be excellent methods added into autonomous driving or ADAS for more safety car. 

In this poster, I will introduce some state-of-art or excellent depth estimation model in the past few years for self-supervised and supervised depth prediction. 


#### Self-Supervised Learning
In seld-supervised depth learning, we can use only the videos from camera for the whole training. But the prediction results are batch noremalized of the output. So the self-supervised depth learning we can get the relative depth in one image, but if we would like to get the absolute depth, we can fuse cameras with radar, LiDAR or similar distance measurement sensor, then multiple current relative depth image with one scale. 

From timeline of self-supervised depth estimation, the mono video depth estimation paper can be seen from the following order. The selected paper I think is excellent. (There are also many excellent paper not included here).
- UCL: **Unsupervised Monocular Depth Estimation with Left-Right Consistency **
- UCB: **Unsupervised Learning of Depth and Ego-Motion from Video**
- Senstime: **GeoNet: Unsupervised Learning of Dense Depth, Optical Flow and Camera Pose**
- Google Brain series 1: **Unsupervised Learning of Depth and Ego-Motion from Monocular Video
Using 3D Geometric Constraints **
- Google Brain series 2: **Unsupervised monocular depth and ego-motion learning with
structure and semantics**
- Google Brain series 3: **Depth Prediction Without the Sensors: Leveraging Structure for Unsupervised
Learning from Monocular Videos**
- Google Brain series 4: **Depth from Videos in the Wild: Unsupervised Monocular Depth Learning from Unknown Cameras**


#### Supervised Learning
In supervised learning, we should get the ground truth depth of the pixel. For example, we can use stereo camera to calculate the disparity, then get the depth. we can use higher beams of LiDAR(128) and simple depth completion to get the sbsolute depth of the camera. 

In supervised depth estimation, the paper [Deep Ordinal Regression Network for Monocular Depth Estimation](http://openaccess.thecvf.com/content_cvpr_2018/html/Fu_Deep_Ordinal_Regression_CVPR_2018_paper.html) got the state-of-art depth estimation accuracy at that time.

#### Basics of depth models


#### Futher reading 
In the 3D computer vision domain, there are many sub-domain using the depth estimation for enhancement. I choose two as an example:
- TUM DVSO: [Deep Virtual Stereo Odometry: Leveraging Deep Depth Prediction for Monocular Direct Sparse Odometry](https://arxiv.org/abs/1807.02570) In this paper, the 