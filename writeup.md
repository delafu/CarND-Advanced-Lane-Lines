## Advanced Lane Finding Project

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

[image1]: ./camera_cal/calibration2.jpg "Distorted Chessboard"
[image2]: ./camera_cal/calibration2_un.jpg "Undistorted Chessboard"
[image3]: ./test_images/straight_lines1.jpg "Test image distorted"
[image4]: ./output_images/straight_lines1.jpg "Test Image undistorted"
[image5]: ./output_images/birdimg2.png "Bird eye of view"
[image6]: ./output_images/birdimgbin.png "Binary bird eye of view"
[image7]: ./output_images/birdimg.png "Transformation"
[image8]: ./output_images/polynomial.png "Polynomial lines"
[image9]: ./output_images/video.png "Video image"


## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first three cells of the IPython notebook located in "./Advanced_Lane.ipynb".  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

#### Distorted Image
![alt text][image1]

#### Undistorted Image

![alt text][image2]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image3]

You can find the process in the python notebook in the third cell. We get the dist and dist results from the calibrateCamera function and we apply to the image in the undistort()

And finally we get:
![alt text][image4]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I tested many combinations of sobel gradients and color transforms in many color spaces and I find that the best transformation to identify lines in the road is the following:

* I don´t use any Sobel. I´ve implemented some of them but I can obtain better results, in my opinion with color transforms.
* I use the combination of different channels of different color spaces
* The first is the L channel from the CIE color space that find almost perfectly the withe lines but don´t find the yellow ones. 
* The second is the B channel from the Lab color space that do a great job identifying the yellow lines but don´t see the withe lines.
* In some tests I used the S channel alone and in combination with the other two channels but I get better results and less recalculations using the window searching of the lines technique in the video 

You can see the code in the fifth and sixth cell of the notebook
#### Original image
![alt text][image5]

#### Binary image
![alt text][image6]


#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

You can find the perspective transform of the images in the 4th cell of the notebook. I´ve decided to use source points by observation and destination points that make the lines almost parallel. 

Theese are the source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 570, 470      | 320, 1        | 
| 722, 470      | 920, 1      |
| 1110, 720     | 920, 720      |
| 220, 720      | 320, 720        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image7]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

This is the tre tricky part of the project. I identify the lines of the projects and fit their positions with a polynomial using two methods. I learned the two methods in the lessons of the web course. The first is using the peaks of the histogram and a sliding window from the peaks detected. This is the blind method

![alt text][image8]

The second method I use is when I´ve detected the lines with the first method. I can search in the neighbour of the lines detected using the blind method. 

I use this method when I get a good fit in the video. To identify what is a good fit I do various sanity checks. I try to assure that the lines are good lines. This is done in the process_lines function of the python notebook.

I know that this code can be improved.

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in the functions calc_curvature and calc_position of the notebook.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in the draw_result function of the notebook.  Here is an example of my result on a test image:

![alt text][image9]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I´ve found a lot of problems in this project. It´s the hardest project and the coolest of my projects in Udacity. I´ve worked hard to smooth the result but I´ve not had enough time to resolve the challenge video. I think that one of the weakness of my algorithm is that it search always for two lines.  
