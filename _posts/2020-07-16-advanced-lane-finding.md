---
title: "Advanced Lane Finding"
categories:
  - Blog
tags:
  - Post
---

The purpose of this post is to explain the theory behind the project [Advanced Lane Finding](https://iacopomc.github.io/projects/2020-07-15-advanced-lane-finding-project/) and build up some intuition about the process. There won't be any snippet from the source code since we only want to build a high level understanding of why we do what we do.

<br/>

Computer Vision is the science of perceiving the world around us through images and it plays a major role in helping vehicles move around and navigate safely.

<br/>

Our goal this time is to write a more sophisticated lane finding algorithm that deals with complex scenarios like curving lines and shadows. We want to measure some of the quantities that we need to know in order to control the car: how much the lanes are curving (useful when you want to steer a car) or where our vehicle is located with respect to the center of the lane.

#### Distortion Correction

Cameras don't create perfect images, some of the objects in the images - especially the ones near the edges - can get stretched or skewed and we have to correct for that. But, why does that happen?

<br/>

*Image distortion* occurs when a camera looks at 3D objects in the real world and transforms them into a 2D image. When light rays pass through the camera lenses they bend at the edges and this creates an effect that distorts the edges of images, so that lines or objects appear more or less curved than they actually are. This is called *radial distortion*.

<br/>

Another type of distortion, is *tangential distortion*, which occurs when a camera lens is not aligned perfectly parallel to the imaging plane with the result that an image looks tilted.
<br/>

Distortion changes what the shape and size of these 3D objects appear to be.

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/distorted_img_1.jpg" width="45%">
<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/distorted_img_2.jpg" width="45%">

<br/>

In these distorted images, we can see that the edges of the chessboard are bent and stretched out. This is a problem because to steer a vehicle we need to know how much the lane is curving, and if the image is distorted we'll get the wrong measure of curvature and our steering angle will be wrong.

So, the first step in analyzing camera images, is to undo this distortion so that we can get correct and useful information out of them.

<br/>

To undistort the images we're going to use the equations:

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/radial_distortion_eq.PNG" width="45%">
<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/tangential_distortion_eq.PNG" width="45%">

<br/>

#### Camera Calibration
We know that distortion changes shape and size of objects in an image. How do we calibrate for that?
<br/>

We basically take pictures of known shapes so we can detect and correct any distortion error. What shapes are we going to use? A chessboard. That's because its regular high contrast pattern makes it easy to detect automatically, and we know what an undistorted chessboard looks like.

<br/>

So, we take multiple pictures of our chessboard against a flat surface

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/calibration4.jpg" width="30%">
<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/calibration11.jpg" width="30%">
<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/calibration18.jpg" width="30%">

<br/>

Then we'll be able to detect any distortion by looking at the difference between the size and shape of these squares in these images and the size and shape that they actually are. We then create a transform that maps these distorted points to undistorted points to undistort any images.

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/point_mapping.PNG" width="30%">

<br/>

The transformation from 3D objects in the real world into a 2D image in cameras is done using a matrix called *camera matrix*. That's what we need to calculate to correct for distortion.

#### Color Transform and Gradient


#### Perspective Transform
To steer a car we need to know how much the lane is curving. To do that, we need to map out the lane in our camera images after transforming them to a different perspective (looking down at the road from above).

#### Find Lane Boundary


#### Curvature of the Lane and Vehicle Position


#### Warp Lane Boundaries Back
