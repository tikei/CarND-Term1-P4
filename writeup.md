## Writeup

---

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

[image1]: ./output_images/original_chessboard.jpg "Original chessboard image"
[image2]: ./output_images/undistorted_output.jpg "Undistorted chessboard image"
[image3]: ./output_images/test2.jpg "Road Image Before Distortion Correction"
[image4]: ./output_images/undistorted_road.jpg "Road Transformed"
[image5]: ./test_images/straight_lines2.jpg "Input Example Image"
[image6]: ./output_images/binary_road.jpg "Binary Thresholded Image"
[image7]: ./output_images/warped_straight_lines.jpg "Warp Example"
[image8]: ./output_images/color_fit_lines.png "Fit Visual"
[image9]: ./output_images/example_output.jpg "Output"
[video1]: ./output_images/project_video_output.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook (located in "./P4.ipynb").  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration using the `calibrate()` function located in the same code cell and distortion coefficients using the OpenCV `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1] 
![alt text][image2]


### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image3] ![alt text][image4]


#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps in IPython notebook code cell 3).  Here's an example of my output for this step.  (note: this is not actually from one of the test images)

![alt text][image5] 
![alt text][image6]


#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `transform_mat()`, which appears in the 5th code cell of the IPython notebook. This function computes a transformation matrix based on `src` source points and `dst` destination points. The same code cell of the IPython notebook contains the function `img_warp()`, which takes as inputs an image (`img`), as well as transformation matrix  (`M`) computed with `transform_mat()`. I chose the source and destination points empirically, by visually inspecting different values and the transformed example images. 


This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 200, 720      | 280, 720      | 
| 580, 480      | 400, 190      |
| 720, 480      | 920, 190      |
| 1080, 720     | 960, 720      |

I verified that my perspective transform was working as expected applying the transformation function to test images and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image7]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I used a sliding window algorithm to identify the pixels that belong to the left and right line (function `line_detect()` in the IPython notebook code cell 7) I fit my lane lines with a 2nd order polynomial like this:

![alt text][image8]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I calculated the radius of the curvature in meters with the `curvature()` function in code cell 11 of the IPython notebook.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in IPython notebook cell 14 using the `img_unwarp()` function. Here is an example of my result on a test image:

![alt text][image9]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./output_images/project_video_output.mp4)

![alt text][video1]

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?


The pipeline I built might fail when there are missing lane markings for a number of frames (as then the average over last n frames will be incorrect), or when glare makes it impossible to detect the lane markings (as in the challenge video). In general if I were to pursue the project further, I would look to improve the color thresholding, experimenting with various color spaces to obtain a more robust detection under difficult lighting conditions.   
