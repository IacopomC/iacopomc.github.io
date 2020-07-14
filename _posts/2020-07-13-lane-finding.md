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

We've taken a greyscale image and using edge detection I turned it into an image full of dots representing edges in the original image. Let's keep in mind that we're looking for lines. To find lines I need to adopt a model of a line and then fit that model to the assortment of dots in my edge detected image. Given that an image is just a function of x and y, I can use the equation of a line y = mx + q. To find the line that passes through all the points making up a lane line I use a Hough Transform.

In Hough space, I can represent my "x vs. y" line as a point in "m vs. b" instead. The Hough Transform is just the conversion from image space to Hough space. So, the characterization of a line in image space will be a single point at the position (m, b) in Hough space.

At the same time, a single point in image space has many possible lines that pass through it, but not just any lines, only those with particular combinations of the m and b parameters. Rearranging the equation of a line, we find that a single point (x,y) corresponds to the line b = y - xm. A single point in Image Space represents a line in Hough space.

What does the intersection point of the two lines in Hough space correspond to in image space?
A line in Image space that passes through both points corresponding to the two lines

So our strategy to find lines in Image space will be to look for interstecting lines in Hough Space. We do this by dividing Housh Space into a grid and define intersecting lines as all lines passing through a given grid a cell.

The problem raises with vertical lines because they have infinite slope in the m,q representation. So, we need a new parametrization: polar coordinates.
rho = the distance of the line from the origin
theta = the angle away from the horizontal

Now each point in image space corresponds to a sign curve in Hough Space (the intersection of the sign curves in Hough Space still represents my line in Image Space).
