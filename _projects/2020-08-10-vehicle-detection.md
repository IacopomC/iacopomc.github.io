---
layout: post
title: "Vehicle Detection"
categories:
  - projects
tags:
  - [Project, Self-Driving, Computer-Vision, Object Detection, Histogram-Oriented-Gradients, Support-Vector-Machine]
date: 2020-08-10
---

The goal of this project is to identify vehicles in a video from a front-facing camera on a car. At first the pipeline is developed on a series of individual images, and later the result is applied to a video stream.

The tools used are (in the following order):

* Perform a *Histogram of Oriented Gradients (HOG)* feature extraction on a labeled training set of images and train a *Linear SVM Classifier*
* Apply a *Color Transform* and append *Binned Color Features*, as well as histograms of color, to the HOG feature vector.
* Implement a *Sliding-Window* technique and use the trained classifier to search for vehicles in images.
* Create a *Heat Map* of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a *Bounding Box* for vehicles detected.

The project is developed using Python and OpenCv. You can download the full code from [GitHub](https://github.com/IacopomC/CarND-Vehicle-Detection).

---
