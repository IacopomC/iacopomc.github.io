---
layout: post
title: "Advanced Lane Finding"
categories:
  - projects
tags:
  - Project
---

The goal of this project is to write a software pipeline to identify the lane boundaries in a video from a front-facing camera on a car. At first the pipeline is developed on a series of individual images, and later the result is applied to a video stream.

The steps followed are:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms and gradients to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

The project is developed using Python and OpenCv. You can download the full code from [GitHub](https://github.com/IacopomC/CarND-Advanced-Lane-Lines).

---
### Camera Calibration

I start by initializing `object points` and `imgpoints`, the arrays containing respectively the 3D points in real world space and the 2D points in image space.

```python
# Array to store object points and image points from all the images
objpoints = [] # 3D points in real world space
imgpoints = [] # 2D points in images plane

# Prepare object points, like (0,0,0), (1,0,0),...
objp = np.zeros((nx*ny,3), np.float32)
objp[:,:2] = np.mgrid[0:nx,0:ny].T.reshape(-1,2) # x, y coordinates
```

Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates that will be appended to `objpoints` every time all chessboard corners are successfully detected in a test image. At the same time, the (x, y) pixel position of each of the corners in the image plane will be appended to `imgpoints`.

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.

```python
for fname in images:
    # Read in each image
    img = cv2.imread(fname)

    # Convert to grayscale
    gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

    # Find the chessboard corners
    ret, corners = cv2.findChessboardCorners(gray, (nx, ny), None)

    # If corners are found, draw corners
    if ret == True:
        imgpoints.append(corners)
        objpoints.append(objp)

# Camera calibration, given object points, image points, and the shape of the grayscale image
ret, mtx, dist, rvecs, tvecs = cv2.calibrateCamera(objpoints, imgpoints, gray.shape[::-1], None, None)
```

I applied this distortion correction to the test image using the `cv2.undistort()` function

```python
def distortion_correction(image, mtx, dist):
  dst = cv2.undistort(image, mtx, dist, None, mtx)
  return dst
```

and obtained this result:

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-project/cal_original.jpg" width="45%">
<img src="{{ site.url }}/assets/images/advanced-lane-finding-project/cal_corrected.jpg" width="45%">

---
### Pipeline

As a first step, I applied the distortion correction to one of the test images:

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-project/test_cal.jpg" width="70%">

<br/>

Then, I used a combination of color and gradient thresholds to generate a binary image:

```python
def color_gradient_transform(img, sobel_size=ksize,
                             grad_thresh=(20, 100), mag_thresh=(30, 100), dir_thresh=(0.7, 1.3),
                             gray_thresh=(180, 255), red_thresh=(200, 255), h_thresh=(15, 100), s_thresh=(90, 255)):
    img = np.copy(img)

    # Gradient transform
    gradient_binary = gradient_transform(img, ksize, grad_thresh=grad_thresh, mag_thresh=mag_thresh, dir_thresh=dir_thresh)

    # Color transform
    color_binary = color_transform(dist_img, gray_thresh=gray_thresh, red_thresh=red_thresh, h_thresh=h_thresh, s_thresh=s_thresh)

    # Combine the two binary thresholds
    combined_binary = np.zeros_like(gradient_binary)
    combined_binary[(color_binary == 1) | (gradient_binary == 1)] = 1

    return combined_binary

    test_trasf = color_gradient_transform(dist_img, ksize,
                                          grad_thresh=(20, 100), mag_thresh=(30, 100), dir_thresh=(0.7, 1.3),
                                          gray_thresh=(180, 255), red_thresh=(200, 255), h_thresh=(15, 100), s_thresh=(90, 255))

```

Here's an example of my output for this step.

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-post/test_trasf.jpg" width="70%">

<br/>

The code for my perspective transform includes a function called `perspective_trasform()` where I chose the hardcode the source and destination points in the following manner:

```python
def perspective_trasform(image):

    img_size = (image.shape[1], image.shape[0])

    # Define source points and destination points
    src = np.float32(
        [[(img_size[0] / 2) - 55, img_size[1] / 2 + 100],
        [((img_size[0] / 6) - 10), img_size[1]],
        [(img_size[0] * 5 / 6) + 60, img_size[1]],
        [(img_size[0] / 2 + 55), img_size[1] / 2 + 100]])
    dst = np.float32(
        [[(img_size[0] / 5), 0],
        [(img_size[0] / 5), img_size[1]],
        [(img_size[0] * 3 / 4), img_size[1]],
        [(img_size[0] * 3 / 4), 0]])

    # Given src and dst points, calculate the perspective transform matrix
    M = cv2.getPerspectiveTransform(src, dst)

    # Calculate inverse perspective matrix
    Minv = cv2.getPerspectiveTransform(dst, src)

    # Warp the image using OpenCV warpPerspective()
    warped = cv2.warpPerspective(image, M, img_size, flags=cv2.INTER_LINEAR)

    # Return the resulting image
    return warped, Minv
```

This resulted in the following source and destination points:

| Source        | Destination   |
|:-------------:|:-------------:|
| 585, 460      | 256, 0        |
| 203, 720      | 256, 720      |
| 1127, 720     | 960, 720      |
| 695, 460      | 960, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-project/test_warped.jpg" width="70%">

<br/>

The result of the previous steps is a binary image where the lane lines stand out clearly. However, I have to decide which pixels are part of the lines and which belongs to the left or right lane.  
To do so, I first compute the histogram of the peaks of where the binary activations occur across the image.

```python
histogram = np.sum(binary_warped[binary_warped.shape[0]//2:,:], axis=0)
```

I grab the half bottom of the image `[binary_warped.shape[0]//2:,:]` because the lanes are most likely to be mostly vertical nearest to the car.

I used a *Sliding Window* placed around the line centers to find and follow the lines up to the top of the frame.

The 5th cell contains the code I used to detect the lane lines.

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-project/fit_img.jpg" width="70%">

<br/>

Next, I measured the radius of curvature:

```python
def measure_curvature_real(ploty, left_fit_cr, right_fit_cr):
    '''
    Calculates the curvature of polynomial functions in meters.
    '''
    # Define conversions in x and y from pixels space to meters
    ym_per_pix = 30/720 # meters per pixel in y dimension
    xm_per_pix = 3.7/700 # meters per pixel in x dimension

    # Define y-value where we want radius of curvature
    # We'll choose the maximum y-value, corresponding to the bottom of the image
    y_eval = np.max(ploty)

    # Calculate the radius of curvature in meters for both lane lines. Should see values of ~1000
    left_curverad = ((1 + (2*left_fit_cr[0]*y_eval*ym_per_pix + left_fit_cr[1])**2)**1.5) / np.absolute(2*left_fit_cr[0])
    right_curverad = ((1 + (2*right_fit_cr[0]*y_eval*ym_per_pix + right_fit_cr[1])**2)**1.5) / np.absolute(2*right_fit_cr[0])

    return left_curverad, right_curverad
```

The parameters to convert from pixels to meters are based on the assumption that the lane is about 30 meters long and 3.7 meters wide. The final radius is given as the average between the right and left lane radius.

Here is an example of the final result on the test image:

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-project/final_img.jpg" width="70%">

---

### Results

Here's the result of the pipeline applied to all the test images included in the project:

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-project/straight_lines1.jpg" width="45%">
<img src="{{ site.url }}/assets/images/advanced-lane-finding-project/straight_lines2.jpg" width="45%">

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-project/test1.jpg" width="45%">
<img src="{{ site.url }}/assets/images/advanced-lane-finding-project/test2.jpg" width="45%">

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-project/test3.jpg" width="45%">
<img src="{{ site.url }}/assets/images/advanced-lane-finding-project/test4.jpg" width="45%">

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-project/test5.jpg" width="45%">
<img src="{{ site.url }}/assets/images/advanced-lane-finding-project/test6.jpg" width="45%">

<br/>

<img src="{{ site.url }}/assets/images/advanced-lane-finding-project/test7.jpg" width="45%">

<br/>

And the final video:

<video width="70%" controls>
  <source type="video/mp4" src="{{ site.url }}/assets/videos/advanced-lane-finding-project/output_video.mp4">
</video>

---

### Shortcomings

One shortcoming occurs when the model doesn't detect one of the two lines in the first frame.

As an improvment smoothing could be applied to avoid that line detection jumps around from frame to frame.
