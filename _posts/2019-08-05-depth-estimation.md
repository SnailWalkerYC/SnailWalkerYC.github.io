---
layout: post
title: Visual Depth Estimation
date: 2019-08-05
categories: [technology]
tags: [cv]
comments: false
---


#### Depth Estimation Conclusion

In 3D computer vision domain, there is one called depth estimation (depth prediction, depth inference), which will predict the depth of the pixel on images. So with this technique, if we have excellent estimation of the pixel depth, we can get dense 2D matrix depth from surroundings. From this aspect, depth estimation of camera capture can be better than LiDAR. LiDAR depth is a kind of sparse beams of depth. Besides, with the help of depth estimation, if the LiDAR is fused with LiDAR, we can use depth completion to get dense and absolute accurate scale 2D dense depth. With the depth information, we can be benefit on tracking, obstacle detection and further path planning. All in all, depth estimation can be excellent methods added into autonomous driving or ADAS for more safety car. 

In this poster, I will introduce some state-of-art or excellent depth estimation model in the past few years for self-supervised and supervised depth prediction. 

***

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

The depth estimation can be concluded as the following problem, we have current pixel position $ p_t $, and the intrisic matrix K, distance to this pixel (depth) $D_{p_t}(p_t)$. The rigid transformation between current frame $t$ to another frame $s$. So we can find the re-projection of this pixel in frame $s$.

$$
\begin{align*}
f(p_t) = K T_{t->s} D_{p_t}(p_t) K^{-1} p_t
\end{align*}
$$

##### UCL-Depth-Estimation

##### UCB-Zhou Depth Estimation

Disadvantage: occulusion is not considered. So it will be not good when meeting with dynamic scenes. I used the inference model on Tinghui Zhou, I found the inference model can be good in depth estimation of road scenarios. But in some cases, like the moving objects, the edge of the objects are not smooth and a little ambiguous. 

##### GeoNet
This paper is from SenseTime research. The contribution of this paper is that there are combined depth estimation, optical flow and ego-motion(pose) estimation framework in unsupervised methodology. From the combinationl task in 3D geometry constrains, the training would be better than single tasks. 

Two stages are used in GeoNet. First stage will get depth prediction and pose estimation, second stage will use the 1st stage results to infer the optical flow. To deal with occlusion problem, consistency check is used, which will only predict the consistent pixel motion. So the first part will focus on the rigid motion, the second state will focus on non-rigid motion. So the first state the DepthNet and PoseNet can be used. These two parts can be fused to rigid motion projection. The second part is a ResFlowNet to learn residuals. To get the training process more robust, consistency is utilized.

To train the rigid structure, the photometric loss and SSIMU loss are utilized. SSIM in some degree can be helpful to eimnate the motion objects. To make the object more distinct, the edge-aware depth smoothness loss is used.

In non-rigid part, there is another part called ResFlowNet to predict the non-rigid motion of the video sequence. Then, sum of the rigid flow and non-rigid flow. Finnaly, one consistency is added into the framework to ignore the outlier during training.

In the paper, there are some basic knowledge needed to know to understand the network. 

- How rigid flow came from:
- What's the input of the ResFlowNet?
- How to train the whole network? How to use it in the total BP? No, this paper divided the sub-network and train respectively, finetune is not working after sub-network training. So I am thinking the paper talked about multi-task will add constrains is not right. The image size is 128x416. The training epochs for the first stage is 30 epochs, and 200 epochs for the second stage. Running time for depth 15 ms, optical 45 ms, pose 4 ms.The optimizer is Adam in this network. But to be honest, I think RMSProp optimizer is better.
- Is inverse warping differentiable? See UCB depth estimation above.

This paper in rigid flow part is the same to UCB depth estimation, and even the depth estimation and pose estimation are the same. So the contribution part is about consistency check, because ResFloeNet is from another paper.

More details of implementation can be seen in [here GeoNet](https://github.com/yzcjtr/GeoNet).


##### Google Brain s1 

##### Google Brain s2 

##### Google Brain s3 

##### Google Brain s4

***

#### Supervised Learning
In supervised learning, we should get the ground truth depth of the pixel. For example, we can use stereo camera to calculate the disparity, then get the depth. we can use higher beams of LiDAR(128) and simple depth completion to get the sbsolute depth of the camera. 

In supervised depth estimation, the paper [Deep Ordinal Regression Network for Monocular Depth Estimation](http://openaccess.thecvf.com/content_cvpr_2018/html/Fu_Deep_Ordinal_Regression_CVPR_2018_paper.html) got the state-of-art depth estimation accuracy at that time.

***

#### Basics of depth models
- Inverse warping: the traditional warping will map the input frame to output frame, but this kind of warping will make the output data misalignment or multiple value in one position. Inverse mapping is the inverse process of this, every pixel data in output frame will find its correspondence in input data. In this situation, inverse warping can eliminate the forward warping problem. During the inverse warping process, the interpolation can be use to get more accurate results.  
- devonconvolution: convolution will make the feature size smaller, which belongs to the decoder process (make data feature dimension smaller); deconvolution will make the input feature size larger. To see the animation, see [here](https://datascience.stackexchange.com/questions/6107/what-are-deconvolutional-layers). 
- Upsampling: the deconvolution is a kind of upsmapling, but there is also up-pooling to resume the size of feature map in decoder process. See [here](https://www.oreilly.com/library/view/deep-learning-for/9781788295628/467cf02b-dc52-49c5-9289-b2721f6758da.xhtml) for more details.
- Multi-scale scheme: 

***

#### Futher reading 
In the 3D computer vision domain, there are many sub-domain using the depth estimation for enhancement. I choose two as an example:
- TUM DVSO: [Deep Virtual Stereo Odometry: Leveraging Deep Depth Prediction for Monocular Direct Sparse Odometry](https://arxiv.org/abs/1807.02570) In this paper, the depth estimation is used to initialize the mono-DSO selected point depth and virtual stereo constrains for outlier filtering.
- Cornell Pseudo LiDAR: [Pseudo-LiDAR from Visual Depth Estimation: Bridging the Gap in 3D Object Detection for Autonomous Driving](https://arxiv.org/abs/1812.07179). In this paper, the raw image is transformed into depth image, then depth images are used for 3D object detection. 
