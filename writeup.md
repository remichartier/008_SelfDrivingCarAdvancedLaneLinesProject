## Advanced Line Project Writeup

---
[//]: # (Image References)

[image1]: ./output_images/011_WriteupIllustrations/2020-11-12_00-17-31_UndistortedChessboard.png "Undistorted"
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"

[Rubric](https://review.udacity.com/#!/rubrics/571/view) Points : Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

Note : All my code is gathered in one unique jupyter notebook file, names "P2_vXX.ipynb" (XX being the version number of this file)

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

- The code for this step in "P2_vXX.ipynb" : in paragraph titled "Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.".
- It is divided in 2 parts : 
  - Chessboard corner detection
  - Camera Calibration
- For Camera Calibration, we define 2 arrays of points, `objpoints[]` for 3d points in real world space, and `imgpoints[]` for 2d points in image plane.
- We have several chessboard images 'camera_cal/calibration*.jpg' taken from different directions, and we'll process them by doing the following : For each chessboard image : 
  - Read it.
  - Using `cv2.findChessboardCorners()`, we'll find coordinates of chessboard corners.
    - Note : we can visualize identified corners overlayed on source image using openCV function `cv2.drawChessboardCorners(img, (9,6), corners, ret)`
  - Store and append the corners coordinates into array `imgpoints[]` (2D projection of the chessboard image).
  - Define ourselves coordinates of the chessboard corners as it should appear on a non distorted grid image, using `numpy.mgrid()` function. Once done, append them to `objpoints[]`.

After processing all those camera calibration chessboard images, we get arrays `imgpoints[]` and `objpoints[]` with all the real corners coordinates of the chessboards and their corresponding expected coordinates in a non distorted grid image of the chessboard.

With that : 
- We apply openCV function `cv2.calibrateCamera(objpoints,imgpoints,(xSize,ySize),None,None)` to get the distorsion matrix and parameters which will be used to undistort any images taken with this particular camera. Cf notebook chapter 'Camera Calibration'
- Use distorsion matrix and parameters to distort any image taken from this camera (cf `cv2.undistort(img, mtx, dist, None, mtx)`) ´ 


I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps at lines # through # in `another_file.py`).  Here's an example of my output for this step.  (note: this is not actually from one of the test images)

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warper()`, which appears in lines 1 through 8 in the file `example.py` (output_images/examples/example.py) (or, for example, in the 3rd code cell of the IPython notebook).  The `warper()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32(
    [[(img_size[0] / 2) - 55, img_size[1] / 2 + 100],
    [((img_size[0] / 6) - 10), img_size[1]],
    [(img_size[0] * 5 / 6) + 60, img_size[1]],
    [(img_size[0] / 2 + 55), img_size[1] / 2 + 100]])
dst = np.float32(
    [[(img_size[0] / 4), 0],
    [(img_size[0] / 4), img_size[1]],
    [(img_size[0] * 3 / 4), img_size[1]],
    [(img_size[0] * 3 / 4), 0]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 585, 460      | 320, 0        | 
| 203, 720      | 320, 720      |
| 1127, 720     | 960, 720      |
| 695, 460      | 960, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines # through # in my code in `my_other_file.py`

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `yet_another_file.py` in the function `map_lane()`.  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  