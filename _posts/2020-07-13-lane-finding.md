---
title: "Finding Lane Lines on the Road"
categories:
  - Blog
tags:
  - Post
---


The goal is to identify and track the position of the lane lines in a series of images. To do so, we can identify 4 useful features:

- Color
- Shape
- Orientation
- Position in the image

One first approach could be trying to find the lane lines using color. To select the color we first need to think about what color actually means in the case of digital images. It means that an image is made up of a stack of three images: one each for Red, Green and Blue (also called channel). These images take values between 0 and 255.
As it happens though, lane lines are not always the same color, and even lines of the same color under different lighting conditions (day, night, etc) may fail to be detected by our simple color selection.
Here's why we need more complex computer vision tools such as Canny Edge Detection:
- First I convert to image to grayscale, and next I compute the gradient. The brightness of each pixel corresponds to the strength of the gradient at that point. We're going to find edges by tracing out the pixels that follow the strongest gradients.

Looking at a greyscale image I see bright points, dark points and all the gray area in between. Rapid changes in brightness are where we find the edges. An image is just a mathematical function of x and y, so I can perform mathematical operations on it like a derivative. Since images are bidimensional, it makes sense to take the derivative with respect to x and y simultaneously. This is what's called a gradient and by computing it I'm calculating how fast pixel values are changing at each point in an image and in which direction they're changing most rapidly.

Computing the gradient gives us thick edges, with the canny algorithm we'll thin out those edges to find just the individual pixels that follow the strongest gradient.
