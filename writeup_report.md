[//]: # (Image References)

[image1]: ./output_images/i1first_cell.png "1cell"
[image2]: ./output_images/i2second_cell.png "2cell"
[image3]: ./output_images/o1.png "o3"
[image4]: ./output_images/i3.png "i3"
[image5]: ./output_images/o2.png "o4"
[image6]: ./output_image/final_result.png "Final"
[image7]: ./test_images/solidWhiteRight.jpg "origin"
[image8]: ./output_image/hough.png "Hough_I"

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