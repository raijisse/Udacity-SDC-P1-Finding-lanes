# **Finding Lane Lines on the Road**

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps.
- Grayscale conversion.
![alt text][grayscale]
[grayscale]: ./assets/grayscale.jpg

- Gaussian smoothing with kernel size = 3

- Canny edge detector to detect strong variations in color
![alt text][canny]
[canny]: ./assets/canny.jpg

- Region of interest selection to focus on probable lanes

- Hough lines transform to detect lines
![alt text][houghlines]
[houghlines]: ./assets/houghlines.jpg

In order to be able to show a single line on the left and right lanes, I modified the draw_lines() function by averaging the slope of the lanes detected.
Concretely, I first divided all detected lines into left (negative slope) and right lane (positive slope) and computed the average slope on each lane. From there, I computed the appropriate coordinates to display the lane from the bottom of the image to the top of our region of interest. In addition, I filtered slopes coefficient in an attempt to reduce outliers.


### 2. Identify potential shortcomings with your current pipeline

Overall, the method we implemented works well in simple cases when the images are well exposed and the lanes to detect are roughly straight and in our area of interest.

- The first issue is the one demonstrated in the challenge video : area of interest is hardcoded, and this is a problem as soon as we change the resolution of the camera and that the coordinates change.

![alt text][hardcodedregion]
[hardcodedregion]: ./assets/hardcodedregion.jpg


- Moreover, region of interest is hardcoded and especially fitted to straight lines. Thus, the same algorithm on a mountain road would likely not work.

- Hard thresholding on canny edge detection is also a problem as we may want to change it depending on the light condition.

- The situations in the examples are easy, but is detecting our current lane enough ? What will happen when we change lane and have a lane in the middle of our region of interest ? Some segments may end up in the supposed left lane and other in the right, messing with our output.


### 3. Suggest possible improvements to your pipeline

- Fix region : a quick hack I implemented in the notebook regarding the resolution of the image problem was to change from pure hard thresholding to image ratio thresholding. For instance, we keep the 90% in the middle of the image at the bottom, and form a pyramid that is a bit flatten at the top...
![alt text][fixhardcodedregion]
[fixhardcodedregion]: ./assets/fixhardcodedregion.jpg
This trick solved most of the problem in the challenge video, although problem number 2 is still here (see below).


- Hardcoded fix thresholds in the various algorithms should be adaptable to the condition. Ex: thresholding in the canny algorithm depending on light condition. In some part of the challenge video, the yellow lane is not very different from the grey road, and the canny algorithm with our thresholds has trouble finding it.


- Straight lines : to remove this barrier we may use more sophisticated methods to fit the lane chunks detected with the canny edge detection. Maybe a polynomial fit for instance ?
