---
title: "Sensor Fusion and Tracking"
categories:
  - Blog
tags:
  - [Post, Self-Driving, Sensor-Fusion, Multi-Object-Tracking, 3d-Point-Cloud, LiDAR, Resnet, Extended-Kalman-Filter]
---

### Abstract

This work proposes a method to detect and track vehicles from
LiDAR maps guided by RGB images. A multitude of applications
depend on the awareness of their surroundings to reason and react accordingly, and the use of LiDAR is crucial for both autonomous vehicles and robotics. This fusion method takes advantage of RGB guidance from a monocular camera to leverage object information and accurately track vehicle from point clouds. This improves the accuracy significantly.

<br/>

A deep-learning approach is used to detect vehicles in LiDAR
data based on a *birds-eye view* perspective of the 3D point-cloud, followed by an extended Kalman filter used to track vehicles over time, based on the lidar detections fused with camera detections.
Two models (Resnet and Darknet) are implemented and their performance evaluated based on precision and recall.

<br/>

This project is based on the project for the course in the *Udacity Self-Driving Car Engineer Nanodegree Program: [Sensor Fusion and Tracking](https://github.com/udacity/nd013-c2-fusion-starter)*. You can download the full code from [GitHub](https://github.com/IacopomC/3D-Multi-Object-Tracking).

### Introduction

Multiple Object Tracking (MOT) is a robot vision problem with
the goal of analyzing videos in order to detect and track objects belonging to one or more categories, without any prior knowledge about the appearance and number of targets [3]. 3D perception as well as precise 3D object detection and tracking are important components in state-of-the-art driving systems [2].

<br/>

This work will focus on self-driving cars, while using LiDAR and monocular RGB images. Here, it is desirable to accurately detect and differentiate objects close as well as far away, and the LiDAR provides the necessary information in the form of a point cloud of its surroundings.

<br/>

The reason 3D detection on point-clouds is challenging is threefold: first, point-clouds are sparse, and most regions of 3D space are without measurements [5]. Second, the resulting output is a three-dimensional box that is often not well aligned with any global coordinate frame. Third, 3D objects come in a wide range of sizes, shapes, and aspect ratios.

<br/>

In this project, measurements from LiDAR and camera are fused
to track vehicles over time using the Waymo Open Dataset [9].

<br/>

The contributions of this paper are:

<ul>
  <li><strong>Object detection</strong>: a deep-learning approach is used to detect vehicles in LiDAR data based on a <i>birds-eye view</i> perspective of the 3D point-cloud. Also, a series of performance measures is used to evaluate the performance of the detection approach.</li>
  <li><strong>Object tracking</strong>: an extended Kalman filter is used to track vehicles over time, based on the lidar detections fused with camera detections. Data association and track managementare implemented as well.</li>
</ul>

The diagram in the following figure contains an outline of the data flow and of the individual steps that make up the algorithm. Two different models based on a *Resnet* and *Darknet* architecture are adopted and their performance on both object detection and tracking evaluated using precision and recall as metrics.

<img src="{{ site.url }}/assets/images/sensor-fusion-tracking-post/project-layout.png">

### Method

The method proposed acts on a projection of a 3D point cloud to a 2D plane, namely *birds eye view*.

#### Object Detection

A deep-learning approach is used to detect vehicles in LiDAR data based on a *birds-eye view* perspective of the 3D point-cloud.

<br/>

**From Range Image to Point Cloud.** Since in the Waymo Open
Dataset the point cloud of each LiDAR is encoded as a range image [9], converting from a range image to a point cloud is the first step of the pipeline.

<br/>

A range image represents a LiDAR point cloud in the spherical
coordinate system based on the following rules:

<ul>
  <li>Each row corresponds to an inclination. Row 0 (top of the image) corresponds to the maximum inclination.</li>
  <li>Each column corresponds to an azimuth. Column 0 (left of the image) corresponds to -x-axis (i.e. the opposite of forward direction). The center of the image corresponds to the +x-axis (i.e. the forward direction). Note that an azimuth correction is needed to make sure the center of the image corresponds to the +x-axis.</li>
</ul>

To compute the conversion we first estimate the inclination
angle for each beam in the range image; we then convert the range image to polar coordinate using the camera projection extrinsic parameters provided.

<br/>

The LiDAR spherical coordinate system is based on the Cartesian
coordinate system in LiDAR sensor frame. A point (x, y, z) in
LiDAR Cartesian coordinates can be uniquely translated to a (range, azimuth, inclination) tuple in LiDAR spherical coordinates.Hence, ultimately we convert polar coordinates to Cartesian coordinates before stacking the point cloud and LiDAR intensity vertically.

<br/>

A preview of the range image

<br/>

<img src="{{ site.url }}/assets/images/sensor-fusion-tracking-post/img_range.JPG">

<br/>

as well as its point cloud representation

<br/>

<img src="{{ site.url }}/assets/images/sensor-fusion-tracking-post/img_pcl.png">

<br/>

are visible here.

**Birds-eye View of LiDAR Data.** After obtaining the point
cloud, we create its Birds-Eye View perspective (BEV). The crucial part is converting the LiDAR points from sensor coordinates to BEV-map coordinates, which can be computed since each sensor comes with an *extrinsic* transformation that defines the transform from the sensor frame to the vehicle frame.

<br/>

A comparison between the two coordinate systems is shown
in the following image: the *LiDAR (or vehicle) coordinate system* is displayed in yellow, while the *image coordinate system* in blue. In the vehicle system the x-axis is positive forward, the y-axis is positive to the left, and the z-axis is positive upward. For the image coordinate system instead the x-axis is positive to the right and the y-axis is positive downward. Not only the axes of the two systems are inverted, but their directions are opposite, as well as their origin shifted (the LiDAR point cloud is centered at the position of the vehicle while the image has its origin at the top left corner).

<br/>

<img src="{{ site.url }}/assets/images/sensor-fusion-tracking-post/lidar_axes.png">

<br/>

To capture fine details and recognize objects more accurately,
instead of casting the values from the point cloud to integers, we modify the level of resolution before assigning them to each pixel.

<br/>

Finally, to compute the BEV map displayed below, we encode
as RGB channels the *intensity*, *height* and *density* layers after normalizing them with the difference between the 1- and 99-percentile to mitigate the influence of outliers.

<br/>

<img src="{{ site.url }}/assets/images/sensor-fusion-tracking-post/img_bev.png">

<br/>

**3D Object Detection.** The Object Detection framework loads
two type of configurations: one from *Super Fast and Accurate 3D Object Detection* based on *3D LiDAR Point Clouds* and the other from Complex-YOLO: Real-time 3D Object Detection on Point Clouds [1, 8]. In the first, the network architecture is based on the *ResNetbased Keypoint Feature Pyramid Network* (KFPN) [4], which provides real-time 3D object detection based on a monocular RGB image [7].

<br/>

The models take a BEV map encoded by height, intensity, and
density of 3D LiDAR point clouds as input, and output the detected bounding boxes in the BEV coordinate space. Before the detections can move along in the processing pipeline, they are converted into metric coordinates in vehicle space.

<br/>

**Performance Evaluation.** At the end, we compute various performance measures to assess the object detection: we evaluate the *Intersection-Over-Union* (IOU) between the ground truth labels and the performed detection to assign a certain detected object to a label based on a threshold. We use this information to compute *precision* and *recall* over all frames.

#### Experiments

#### Conclusion

#### References

