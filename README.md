**Overview**

The main idea of this project is to detect lanes using computer vision techniques. This project is part of the Udacity Self-Driving Car Nanodegree.

The following steps were performed for lane detection:

1. Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
2. Apply a distortion correction to raw images.
3. Use color transforms, gradients, etc., to create a thresholded binary image.
4. Apply a perspective transform to rectify binary image ("birds-eye view").
5. Detect lane pixels and fit to find the lane boundary.
6. Determine the curvature of the lane and vehicle position with respect to center.
7. Warp the detected lane boundaries back onto the original image.
8. Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

**Camera calibration**

The camera was calibrated using the chessboard images in 'camera_cal/*.jpg'. The following steps were performed for each calibration image:

1. Convert to grayscale
2. Find chessboard corners with OpenCV's findChessboardCorners() function, assuming a 9x6 board
3. After the above steps were executed for all calibration images, I used OpenCV's calibrateCamera() function to calculate the distortion matrices. Using the distortion matrices, I undistort images using OpenCV's undistort() function.

To illustrate, the following is the calibration image 'camera_cal/calibration10.jpg':
![Image of calcamera](https://github.com/SSN15/Project-Advanced-Lane-Finding/blob/master/camera_cal/calibration10.jpg)

The final calibration matrices are saved in the pickle file 'calibrate_camera.p'

**Lane detection**

1. Input original image as shown below
![Image of oriimg](https://github.com/SSN15/Project-Advanced-Lane-Finding/blob/master/test_images/straight_lines2.jpg)

2. Undistort the image using the parameters stored in 'calibrate_camera.p'
The undistorted image is shown below
![Image of undistort](https://github.com/SSN15/Project-Advanced-Lane-Finding/blob/master/output_images/undistort_straight_lines2.png)

All the undistorted images are stored in 'Project-Advanced-Lane-Finding/output_images/'

3. Threshold binary image is obtained by feeding in the undistorted image.
Following steps are performed to obtain threshold binary image
- Apply the following filters with thresholding, to create separate "binary images" corresponding to each individual filter
  * Absolute horizontal Sobel operator on the image
  * Sobel operator in both horizontal and vertical directions and calculate its magnitude
  * Sobel operator to calculate the direction of the gradient
  * Convert the image from RGB space to HLS space, and threshold the S channel
 - Combine the above binary images to create the final binary image
 The images of each threshold operation and the combined binary image is shown below
 
 ![Image of threshbinary](https://github.com/SSN15/Project-Advanced-Lane-Finding/blob/master/output_images/threshold.png)
 
 4. The next step is perspective transform. The goal of this step is to get the "bird's eye view" of the lane, which enables us to fit a curved line to the lane lines (e.g. polynomial fit). Another thing this accomplishes is to "crop" an area of the original image that is most likely to have the lane line pixels. To accomplish the perspective transform, I use OpenCV's getPerspectiveTransform() and warpPerspective() functions.
 
 An example image after applying perspective transform is shown below
 
 ![Image of perspective](https://github.com/SSN15/Project-Advanced-Lane-Finding/blob/master/output_images/warped_straight_lines2.png)
 
 **Polynomial fit**
 From the binary warped image, I now fit a 2nd order polynomial to both left and right lane lines. In particular, I perform the following:
* Calculate histogram for the bottom half of the image
* Partition the image into 9 horizontal slices
* Starting from the bottom slice, enclose a 200 pixel wide window around the left peak and right peak of the histogram (split the histogram in half vertically)
* Go up the horizontal window slices to find pixels that are likely to be part of the left and right lanes, recentering the sliding windows opportunistically
* Given 2 groups of pixels (left and right lane line candidate pixels), fit a 2nd order polynomial to each group, which represents the estimated left and right lane lines

The output of polynomial fit is shown below

![Image of polyfit](https://github.com/SSN15/Project-Advanced-Lane-Finding/blob/master/output_images/polyfit_test2.png)
  
 **Radius of curvature**
 Given the polynomial fit for the left and right lane lines, I calculated the radius of curvature for each line according to formulas. I also converted the distance units from pixels to meters, assuming 30 meters per 720 pixels in the vertical direction, and 3.7 meters per 700 pixels in the horizontal direction.

The final annotated image is shown below
![Image of annotated](https://github.com/SSN15/Project-Advanced-Lane-Finding/blob/master/output_images/annotated_test2.png)

**Lane detection for Video**

Input the video file project_video..mp4
Run the code Lanedetection_video.py and the result video is stored as out.mp4
The pipeline works well for this video.

However for the video file challenge_video.mp4, the result is not as robust as the previous vide.
The expected performance can be achieved by building a deep learning model!


