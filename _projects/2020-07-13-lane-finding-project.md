---
layout: post
title: "Project: Finding Lane Lines on the Road"
categories:
  - blog
tags:
  - Project
---

## Description

The goal of this project is to identify lane lines on the road. At first the pipeline is developed on a series of individual images, and later the result is applied to a video stream.

The tools used are (in the following order):
* Grayscaling
* Gaussian smoothing
* Color selection
* Region of interest selection
* Canny Edge Detection
* Hough Tranform line detection

The project is developed using Python and OpenCv. It can be found in the file `P1.ipynb`

---

### Pipeline

Following, the steps of the pipeline on a test image

<img src="{{ site.url }}/assets/images/lane-finding-project/solidWhiteCurve.jpg">

First, I converted the image to *grayscale*

<img src="{{ site.url }}/assets/images/lane-finding-project/grayscale.jpg">

Then I applied the *Gaussian Smoothing* filter using a kernel size of 5 to get rid of noise

<img src="{{ site.url }}/assets/images/lane-finding-project/blur_gray.jpg" width="50%">

After that, I used *Color Selection* to highlight the lane lines only

<img src="{{ site.url }}/assets/images/lane-finding-project/highlighted_img.jpg" width="50%">

The *Canny Edge Operator* with a low threshold of 50 and  high threshold of 150 helped me detect edges

<img src="{{ site.url }}/assets/images/lane-finding-project/edges.jpg" width="50%">

And through the use of a *Trapezoidal Mask* I isolated only the lane lines

<img src="{{ site.url }}/assets/images/lane-finding-project/masked_img.jpg" width="50%">

Following, I used a *Hough Transform* to detect the lines with parameters:
* rho = 1
* theta = pi/180
* threshold = 30 *minimum number of votes (intersections in Hough grid cell)*
* min_line_len = 40 *minimum number of pixels making up a line*
* max_line_gap = 100 *maximum gap in pixels between connectable line segments*

<img src="{{ site.url }}/assets/images/lane-finding-project/line_image.jpg" width="50%">

To draw a single line on the left and right lanes, I modified the *draw_lines()* function by separating line segments by their slope to decide which segments are part of the left line vs. the right line. During this process, I selected only those lines whose angle fell between 20 and 45 degrees, ignoring possible horizontal and vertical segments that could alter the average.

```python
for line in lines:
  for x1, y1, x2, y2 in line:
    slope = ((y2-y1)/(x2-x1))

    # calculate angle to get rid of possible horizontal and vertical lines
    angle = np.arctan2(y2 - y1, x2 - x1) * 180. / np.pi
    if (angle > 20 and angle < 45) or (angle > -45 and angle < -20):
      intercept = y1 - slope * x1
      if slope > 0:
        left_slope.append(slope)
        left_intercept.append(intercept)
      else:
        right_slope.append(slope)
        right_intercept.append(intercept)
```

Then, I averaged the position of each of the lines and extrapolated to the top and bottom of the lane.

```python
avg_left_slope = np.mean(left_slope)
avg_left_intercept = np.mean(left_intercept)

avg_right_slope = np.mean(right_slope)
avg_right_intercept = np.mean(right_intercept)

ytop = ytop
xtop_left = np.round((ytop - avg_left_intercept)/avg_left_slope).astype(int)
xtop_right = np.round((ytop - avg_right_intercept)/avg_right_slope).astype(int)

ybottom = img.shape[1]
xbottom_left = np.round((ybottom - avg_left_intercept)/avg_left_slope).astype(int)
xbottom_right = np.round((ybottom - avg_right_intercept)/avg_right_slope).astype(int)

```

<img src="{{ site.url }}/assets/images/lane-finding-project/final_output.jpg" width="50%">

Finally, I applied the same pipeline to 3 different video streams included in the folder ```test_video_output```

---
### Shortcomings with current pipeline


Potential shortcomings would occur with changing in lighting, presence of shadows and imperfections in the asphalt of the roads

---

### Possible improvements

A possible improvement would be to predict the most probable area where the lines will be based on the previous frame rather than recomputing the whole pipeline for each frame.

Another potential improvement could be to use a smoothing method to get rid of the flickering effect of the lines between one frame and another
