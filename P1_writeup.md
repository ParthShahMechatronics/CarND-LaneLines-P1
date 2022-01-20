# **Finding Lane Lines on the Road** 


**Lane Detection**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

---

### Reflection

### 1. Pipeline used for lane detection
The pipeline consisted of 8 steps. These steps are listed as below:
#### a. Color selection and thresholding
The first step of the pipeline was filtering out the input image based on a range of colors. A white filter was developed using the RGB color range and a Yellow filter was developed using an HVS scale. The lower threshold for the yellow color was (15,90,20) and the higher threshold was (25, 255, 255). These two filters were combined and colors outside this range were masked as black pixels. The images below show the result of this color selection and thresholding on the images of the road.

<img src = "./test_images_output/mask_whiteCarLaneSwitch.jpg" height="50%" width="50%" >
<img src = "./test_images_output/mask_solidWhiteRight.jpg" height="50%" width="50%" /> 

#### b. Grayscale and Gaussian Smoothing
To further filter the image for a more narrowed lane search. A grayscale filter was applied that converts the image into one color channel. To reduce the noise in the image, a gaussian filter (blur box filter) is then applied with a kernel size of 15. A high kernel size was chosen to prevent aliasing. 

#### c. Canny edge detection
Canny edge detection is used to determine edges by computing gradients on a grayscale, smooth image. Once the gradient magnitude and orientation of the pixels are identified, the algorithm uses a non-maximum suppression technique to discretize the gradient orientation and suppress the values that are not of maximum inorder to look for thin edges. Hysteresis thresholding then helps to identify strong and weak edges. If a weak edge is connected to a strong edge it will be added to the edge otherwise it will be discarded. Caanny edge detection gives single-pixel-wide edges with good continuation between adjacent pixels. 

<img src = "./test_images_output/canny_solidYellowLeft.jpg" height="50%" width="50%" /> 

#### d. Region of Interest Selection
Once the edges of the image are obtained, the search space is contained using a trapezoidal (polygon) approach to narrow down the area in which the lanes will be located. This will help us localize the search for the lane lines and reduce interference. 

#### e. Line detection, classification and fitting
cv2.HoughLinesP is used to obtain coordinates of the line in the hough space. Strong support fora line in image space maps to where many lines intersect in hough space. These values are then used to draw the full lane lines. In order to draw a single line on the left and right lanes, the draw_lines() function was modified by calculating the slope of the edges to differentiate between the left and right lines. Based on the side, a line of best fit was determined with use of the RANSACRegressor function. This facilitated the removal of any anomalies/outliers in the dataset of points, and linear regression was used to map the inliers to the line of best fit. This line was then extrapolated accordingly, for either lane side, and the lanes lines were then drawn. A sample output of the full hough lines is shown below:

<img src = "./test_images_output/hough_solidWhiteCurve.jpg" height="50%" width="50%" /> 

#### f. Final results
The final output image overlays the original image with the lane lines drawn. Example of these are presented below:

<img src = "./test_images_output/solidWhiteRight.jpg" height="50%" width="50%" /> 
<img src = "./test_images_output/solidYellowCurve2.jpg" height="50%" width="50%"> 


### 2. Identify potential shortcomings with your current pipeline

Although the pipeline was effective in finding the lanes for the images and the straight roads with white and yellow lines, one shortcoming that was observed was when applied to roads with curves, the line fitting was not accurate. 

Another potential shortcoming would be what would happen when the lighting conditions of the road change such as at night. This would affect the color filters being applied and may result in some missing line information. It is also possible for the lines to be missed under conditions where a large vehicle conducts a lane switch infront of the camera and obstructs the full path of the line. This would make it harder to predict the direction and orientation of the lines. 

### 3. Suggest possible improvements to your pipeline

A possible improvement would be to line size by finding a more accurate region of interest. The trappezoidal shape works for this road condition but a higher degree shape could be more fitting. 

Another potential improvement could be to utilize and adaptive model for linear regression. This would adapt the parameters based on the input region and can also be used with locally weighted models. A DNN would also be effective in improving the performance of the pipeline. 
