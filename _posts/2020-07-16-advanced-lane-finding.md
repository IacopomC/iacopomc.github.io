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

#### Camera Calibration
The transformation from 3D objects in the real world into a 2D image in cameras is done using a matrix called *camera matrix*. That's what we need to calculate to correct for distortion.

#### Distortion Correction

Cameras don't create perfect images, some of the objects in the images - especially the ones near the edges - can get stretched or skewed and we have to correct for that. But, why does that happen?

<br/>

*Image distortion* occurs when a camera looks at 3D objects in the real world and transforms them into a 2D image. Distortion changes what the shape and size of these 3D objects appear to be.

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/distorted_img_1.jpg" width="45%">
<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/distorted_img_2.jpg" width="45%">

<br/>

In these distorted images, we can see that the edges of the chessboard are bent and stretched out. This is a problem because to steer a vehicle we need to know how much the lane is curving, and if the image is distorted we'll get the wrong measure of curvature and our steering angle will be wrong.

So, the first step in analyzing camera images, is to undo this distortion so that we can get correct and useful information out of them.

#### Color Transform and Gradient


#### Perspective Transform
To steer a car we need to know how much the lane is curving. To do that, we need to map out the lane in our camera images after transforming them to a different perspective (looking down at the road from above).

#### Find Lane Boundary


#### Curvature of the Lane and Vehicle Position


#### Warp Lane Boundaries Back
