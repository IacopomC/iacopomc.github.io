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

<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/distorted_img_1.jpg" width="49%">
<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/distorted_img_2.jpg" width="49%">

<br/>

In these distorted images, we can see that the edges of the chessboard are bent and stretched out. This is a problem because to steer a vehicle we need to know how much the lane is curving, and if the image is distorted we'll get the wrong measure of curvature and our steering angle will be wrong.

So, the first step in analyzing camera images, is to undo this distortion so that we can get correct and useful information out of them.

<br/>

To undistort the images we're going to use the equations:

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/radial_distortion_eq.PNG" width="49%">
<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/tangential_distortion_eq.PNG" width="49%">

<br/>

#### Camera Calibration
We know that distortion changes shape and size of objects in an image. How do we calibrate for that?
<br/>

We basically take pictures of known shapes so we can detect and correct any distortion error. What shapes are we going to use? A chessboard. That's because its regular high contrast pattern makes it easy to detect automatically, and we know what an undistorted chessboard looks like.

<br/>

So, we take multiple pictures of our chessboard against a flat surface

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/chessboard1.jpg" width="32%">
<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/chessboard2.jpg" width="32%">
<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/chessboard3.jpg" width="32%">

<br/>

This way, we'll be able to detect any distortion by looking at the difference between the size and shape of these squares in these images and the size and shape that they actually are. We then create a transform that maps these distorted points to undistorted points to correct any images.

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/point_mapping.PNG">

<br/>

It's recommended to use at least 20 images to obtain a reliable calibration.

#### Lane Curvature and Vehicle Position
Great! Now, we prepared our images. Let's keep in mind what our goal is: detect lane lines, measure their curvature and the vehicle position with respect to the center.

<br/>

We can start by tackling the problem of extracting one really important piece of information from our images: the *lane curvature*. This is important because to steer a car we need to know how much the lane is curving (together with the speed and dynamics of the car).

<br/>

To calculate the curvature of a lane line, we're going to fit a 2nd degree polynomial to that line. For a lane line that is close to vertical, we can fit a line using this formula:

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/lane_line_fit_eq.PNG" width="40%">

<br/>

where *A* gives us the curvature we're looking for, *B* gives us the direction that the line is pointing towards, and *C* gives us the position of the line based on how far away it is from the left side of the image (y = 0).

#### Gradient Transform
Now we know what we need, but how are we going to find it? We'll be using something called *Perspective Transform*, but before that there are a couple of image preprocessing steps that we will take to make our job easier: we'll use color and gradient transforms to create a thresholded binary image.

<br/>

In the previous project [Finding Lane Lines on the Road](https://iacopomc.github.io/blog/lane-finding/) we used the *Canny Edge Detector* to find pixels that were likely to be part of a line in an image. This algorithm is great to find all possible lines, but for lane detection it gives us a lot of edges from scenery and cars that we ended up discarding.
<br/>

To speed up the process we can take advantage of the fact that the lane lines tend to be close to vertical. How can we take advantage of it? We can use gradients only to find steep edges that are more likely to be lanes in the first place.

<br/>

One operator used to calculate x and y gradients is the *Sobel Operator*, and if we applied it on an image and took the absolute value:

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/sobel_x.jpg" width="49%">
<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/sobel_y.jpg" width="49%">

<br/>

we could see that the gradients taken in both the x and y directions detect the lane lines and pick up other edges. Taking the gradient in the x direction emphasizes edges closer to vertical while the gradient in the y direction emphasizes edges closer to horizontal.

<br/>

The x-gradient does a cleaner job of picking up the lane lines, but we can see the lines in the y-gradient as well. Given the results, it's worth exploring other properties of the gradient like *magnitude* and *direction*:

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/gradx.jpg" width="49%">
<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/grady.jpg" width="49%">

<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/mag_binary.jpg" width="49%">
<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/dir_binary.jpg" width="49%">

<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/combined.jpg" width="49%">

<br/>

While all these properties are able to identify lane lines, it's by combining them all together that we obtain the most out of them to isolate lane-line pixels.

<br/>

It's worth noticing that  we can modify the kernel size for the *Sobel Operator* to change the size of this region. Taking the gradient over larger regions can smooth over noisy intensity fluctuations on small scales.
<br/>

As a general rule, the kernel size should be an odd number. Since we are searching for the gradient around a given pixel, we want to have an equal number of pixels in each direction of the region from this central pixel, leading to an odd-numbered filter size.

#### Color Transform
When we apply the steps above, one crucial part is converting the image to grayscale. By doing so, we lose some valuable information:

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/regular_view_road.jpg" width="32%">
<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/grayscale.jpg" width="32%">
<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/s_channel.jpg" width="32%">

<br/>

In the grayscale image the yellow line on the left is barely visible, while in the s channel it's way more clear.

#### Perspective Transform
A *Perspective Transform* maps the points in a given image to different, image points with a new perspective.
<br/>

It essentially warps the image by dragging points towards or away from the camera to change the apparent perspective.

<br/>

Why are we interested in doing a *Perspective Transform*? Because in reality road boundaries are parallel to each other while in the 2D perspective view they seem to converge to a common point. The perspective projection effect has distorted the shape of the actual road boundaries. Since we ultimately want to measure the curvature of the lines we need to transform to a top-down view, that's when our lane lines will look parallel and we will be able to fit them with the formula above.

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/regular_view_road.jpg" width="49%">
<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/top_down_view_road.jpg" width="49%">

<br/>

The process of applying a perspective transform is similar to what we did to correct image distortion, but this time we want to map the points in our image to different desired image points with a new perspective.

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/perspective_transform_points.jpg">

<br/>

Doing a the bird's eye-view transform is especially useful in this case because it will allow us to match our car location with a map.

#### Find Lane Boundary


#### Warp Lane Boundaries Back
