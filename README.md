**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./camera_cal/calibration1.jpg "Original"
[image2]: ./output_images/undistort_output.png "Undistorted"

###Camera Calibration

I used the chessboard images in the 'camera_cal' directory to calibrate the camera. These images are various chessboard images taken from different angles.

First, I changed the image to grayscale and found the "image points" using opencv's `findChessboardCorners()` function. Assuming a board of size 9x6, I could not find the corner in some images, so I finally used the board sizes of [(9,6),(9,5),(6,5),(7,5)]

Next, I prepared "object points", which will be the (x,y,z) coordinates of the chessboard corners in the world. Because need to prepare different points according to board size, I defined a function called `get_object_points()`.

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result:

| Original           | Undistorted           |
|--------------------|-----------------------|
|![Original][image1] | ![Undistorted][image2]|

Then I stored calculated matrix and distortion coefficients in the pickle file to calibrate other images taken with this camera later.