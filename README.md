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
[image3]: ./test_images/test1.jpg "Test Original"
[image4]: ./output_images/undistort_test_output.png "Test Undistorted"
[image5]: ./output_images/combined_thresh.png "Combined Binary"
[image6]: ./output_images/combined_thresh_roi.png "ROI"
[image7]: ./output_images/source_points.png "Source points"
[image8]: ./output_images/warped.png "Warped"
[image9]: ./output_images/binary_warped.png "Binary warped"

###Camera Calibration

I used the chessboard images in the 'camera_cal' directory to calibrate the camera. These images are various chessboard images taken from different angles.

First, I changed the image to grayscale and found the "image points" using OpenCV's `findChessboardCorners()` function. Assuming a board of size 9x6, I could not find the corner in some images, so I finally used the board sizes of [(9,6),(9,5),(6,5),(7,5)]

Next, I prepared "object points", which will be the (x,y,z) coordinates of the chessboard corners in the world. Because need to prepare different points according to board size, I defined a function called `get_object_points()`.

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result:

| Original          | Undistorted          |
|-------------------|----------------------|
|![Original][image1]|![Undistorted][image2]|

Then I stored calculated matrices and distortion coefficients in the pickle file to calibrate other images taken with this camera later.

###Pipeline (single images)

#### Undistort image

Using stored matrices and distortion coefficients in the pickle file, I can undistort the input image.

| Original          | Undistorted          |
|-------------------|----------------------|
|![Original][image3]|![Undistorted][image4]|

#### Thresholded binary image

I used a various combinations of color and gradient thresholds to generate a binary image where the lane lines are clearly visible.

Apply the following filters:
  * Takes an image, gradient orientation, and threshold min/max values
  * Return the magnitude of the gradient for a given sobel kernel size and threshold values
  * Return the direction of the gradient for a given sobel kernel size and threshold values
  * Convert RGB to HLS and threshold to binary image using S channel
  * Combine the above binary images to create the final binary image

Here's an example of my output for this step.

| Original          | Thresholding    |
|-------------------|-----------------|
|![Original][image3]|![Binary][image5]|

Only keeps the region of interest similar to project 1, the image defined by the polygon formed from `vertices`. The rest of the image is set to black.

| Thresholding    | Region of interest|
|-----------------|-------------------|
|![Binary][image5]|![ROI][image6]     |

#### Perspective Transform

Next, I need to identify four source points for your perspective transform. The easiest way to do this is to investigate an image where the lane lines are straight, and find four points lying along the lines that, after perspective transform, make the lines look straight and vertical from a bird's eye view perspective.

This resulted in the following source and destination points:

| Source  | Destination |
|---------|-------------|
| 200,720 | 300,720     |                
| 595,450 | 300,0       |
| 685,450 | 980,0       |
|1100,720 | 980,720     |

I hard-coded the source and destination points for the perspective transform. To accomplish the perspective transform, I use OpenCV's `getPerspectiveTransform()` and `warpPerspective()` functions.

| Source points   | Perspective Transform | Thresholding    |
|-----------------|-----------------------|-----------------|
|![points][image7]|![warped][image8]      |![binary][image9]|