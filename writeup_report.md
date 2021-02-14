[//]: # (Image References)

[image1]: ./output_images/i1first_cell.png "1cell"
[image2]: ./output_images/i2second_cell.png "2cell"
[image3]: ./output_images/o1.png "o3"
[image4]: ./output_images/i3.png "i3"
[image5]: ./output_images/o2.png "o4"
[image6]: ./output_images/o3.png "o5"
[image7]: ./output_images/o4.png "o6"
[image8]: ./output_images/o5.png "o7"
[image9]: ./output_images/o6.png "o8"
[image10]: ./output_images/o7.png "o9"
[image11]: ./output_images/o8.png "o10"
[image12]: ./output_images/i4.png "i4"
[image13]: ./output_images/o9.png "o11"
[image14]: ./output_images/i5.png "i5"
[image15]: ./output_images/o10.png "o12"
[image16]: ./output_images/i6-1.png "i6-1"
[image17]: ./output_images/i6-2.png "i6-2"

# Self-Driving Car Engineer Nanodegree

## **Advanced Lane Finding on the Road** 

![Cover](./output_images/cover.jpg)

## Overview

This project is Advanced Lane Finding on the Road in Udacity Self-Driving Car Engineer Nanodegree

## Goals/Steps

My project consisted of 9 steps:

1. camera calibration using chessboard images,
2. undistort image,
3. create a thresholded binary image,
4. Use a perspective transform to wrap image,
5. Detect lane pixels and fit to find the lane boundary.,
6. Find the curvature of the lane and vehicle position with respect to center,
7. Warp the detected lane boundaries back onto the original image,
8. Display numerical estimation of lane curvature and vehicle position,
9. Run pipeline in a video.

### import all package that needed in the project

- [OpenCV](https://opencv.org/) - an open source computer vision library,
- [Matplotbib](https://matplotlib.org/) - a python 2D plotting libray,
- [Numpy](http://www.numpy.org/) - a package for scientific computing with Python,
- [MoviePy](http://zulko.github.io/moviepy/]) - a Python module for video editing.

the import code shown in image below

![alt text][image1]

### Step 1: camera calibration using chessboard images

The next step is to perform a camera calibration. A set of chessboard images will be used for this purpose.

I have defined the `calibrate_camera` function which takes as input parameters an array of paths to chessboards images, and the number of inside corners in the _x_ and _y_ axis.

For each image path, `calibrate_camera`:
- reads the image by using the OpenCV [cv2.imread](https://docs.opencv.org/3.0-beta/doc/py_tutorials/py_gui/py_image_display/py_image_display.html) function, 
- converts it to grayscale usign [cv2.cvtColor](https://docs.opencv.org/3.0.0/df/d9d/tutorial_py_colorspaces.html), 
- find the chessboard corners usign [cv2.findChessboardCorners](https://docs.opencv.org/2.4/modules/calib3d/doc/camera_calibration_and_3d_reconstruction.html?highlight=calib)

Finally, the function uses all the chessboard corners to calibrate the camera by invoking [cv2.calibrateCamera](https://docs.opencv.org/3.0-beta/doc/py_tutorials/py_calib3d/py_calibration/py_calibration.html).

The values returned by `cv2.calibrateCamera` will be used later to undistort our video images.

The picture below is complete code for this step can be found in the second code cell 

![alt text][image2]

### Step 2: undistort image

Another OpenCv funtion, [cv2.undistort](https://docs.opencv.org/3.0-beta/doc/py_tutorials/py_calib3d/py_calibration/py_calibration.html), will be used to undistort images.

Below, it can be observed the result of undistorting one of the chessboard images:

![alt text][image3]

The image below show code for this step

![alt text][image4]

### Step 3: create a thresholded binary image.

In this step, we will define the following funtions to calculate several gradient measurements (x, y, magnitude, direction and color).

- Calculate directional gradient: `abs_sobel_thresh()`.
- Calculate gradient magnitude: `mag_thres()`.
- Calculate gradient direction: `dir_thresh()`.
- Calculate color threshold: `col_thresh()`.

Then, `combine_threshs()` will be used to combine these thresholds, and produce the image which will be used to identify lane lines in later steps.

Below, I have copied the result of applying each function to a sample image:

- Calculate directional gradient for _x_ and _y_ orients:

![alt text][image5]
![alt text][image6]

- Calculate gradient magnitude

![alt text][image7]

- Calculate gradient direction

![alt text][image8]

- Calculate color threshold

![alt text][image9]

The output image resulting of combining each thresh can be observed below:

![alt text][image10]

### Step 4: Use a perspective transform to wrap image.

The next step in our pipeline is to transform our sample image to _birds-eye_ view.

The process to do that is quite simple:

- First, you need to select the coordinates corresponding to a [trapezoid](https://en.wikipedia.org/wiki/Trapezoid) in the image, but which would look like a rectangle from _birds_eye_ view.
- Then, you have to define the destination coordinates, or how that trapezoid would look from _birds_eye_ view. 
- Finally, Opencv function [cv2.getPerspectiveTransform](https://docs.opencv.org/2.4/modules/imgproc/doc/geometric_transformations.html#getperspectivetransform) will be used to calculate both, the perpective transform _M_ and the inverse perpective transform _Minv.
- _M_ and _Minv_ will be used respectively to warp and unwarp the video images.

Please find below the result of warping an image after transforming its perpective to birds-eye view:

![alt text][image11]

The code for the `warp()` function can be found below:

![alt text][image12]

 Please notice that the function does not return the unwarped version of the image. That would be performed in a later step.

 ### Step 5: Detect lane pixels and fit to find the lane boundary.

 In order to detect the lane pixels from the warped image, the following steps are performed.
 
 - First, a histogram of the lower half of the warped image is created. Below it can be seen the histogram and the code used to produce it.

![alt text][image13]

![alt text][image14]

- Then, the starting left and right lanes positions are selected by looking to the max value of the histogram to the left and the right of the histogram's mid position.
- A technique known as _Sliding Window_ is used to identify the most likely coordinates of the lane lines in a window, which slides vertically through the image for both the left and right line.
- Finally, usign the coordinates previously calculated, a second order polynomial is calculated for both the left and right lane line. Numpy's function [np.polyfit](https://docs.scipy.org/doc/numpy/reference/generated/numpy.polyfit.html) will be used to calculate the polynomials.

Please find below the result of applying the `detect_lines()` function to the warped image:

![alt text][image15]

The image below show code for this step

![alt text][image16]
![alt text][image17]

Once you have selected the lines, it is reasonable to assume that the lines will remain there in future video frames.
`detect_similar_lines()` uses the previosly calculated _line_fits_ to try to identify the lane lines in a consecutive image. If it fails to calculate it, it invokes `detect_lines()` function to perform a full search.