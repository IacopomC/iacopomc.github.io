---
title: "Sensor Fusion and Tracking"
categories:
  - Blog
tags:
  - [Post, Self-Driving, Sensor-Fusion, Multi-Object-Tracking, 3d-Point-Cloud, LiDAR, Resnet, Extended-Kalman-Filter]
---

#### Abstract

This work proposes a method to detect and track vehicles from
LiDAR maps guided by RGB images. A multitude of applications
depend on the awareness of their surroundings to reason and react accordingly, and the use of LiDAR is crucial for both autonomous vehicles and robotics. This fusion method takes advantage of RGB guidance from a monocular camera to leverage object information and accurately track vehicle from point clouds. This improves the accuracy significantly.

<br/>

A deep-learning approach is used to detect vehicles in LiDAR
data based on a birds-eye view perspective of the 3D point-cloud, followed by an extended Kalman filter used to track vehicles over time, based on the lidar detections fused with camera detections.
Two models (Resnet and Darknet) are implemented and their performance evaluated based on precision and recall.

<br/>

This project is based on the project for the course in the Udacity Self-Driving Car Engineer Nanodegree Program: [Sensor Fusion and Tracking](https://github.com/udacity/nd013-c2-fusion-starter). You can download the full code from [GitHub](https://github.com/IacopomC/3D-Multi-Object-Tracking).