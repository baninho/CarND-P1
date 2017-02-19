#**Finding Lane Lines on the Road** 


---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

---

### Reflection

###1. Description

My pipeline consisted of 7 steps:
1. Color selection: I use color selection to only yellow or white objects in the image
2. Grayscale conversion: Converting to grayscale enables me to treat both white and yellow lines the same
3. Blur: Gaussian Blur to remove noise
4. Canny Edge Detection: Detect edges in the image by the gradient
5. Region of Interest Masking: Applying a trapezoid region mask gets rid of peripheral objects
6. Hough Transform: Extract lines using Hough Transform function
7. Line Averaging and Extrapolation: PT1 and outlier filtering and gradient limits are used to get a robust result


I filter, average and extrapolate the line segments in mark_lanes() outside of draw_lines() to be able to easily switch between raw line markings and single lane markings for calibration purposes. Global variables are used to filter based on previous frames, as lanes are not likely to change abruptly.

First, lines with little change in their y-value are discarded. Horizontal lines or very short lines are unlikely to belong to lane lines and unlikely to help finding their direction. Lines outside a plausible range of slopes are discarded, as lane lines tend to have similar slope. If all lines have been discarded, the line used in the last frame is reused. Polyfit is used to perform regression on the found lines to yield the most likely lane line. Start and end points of the single lane line are calculated and drawn onto the image.


###2. Potential shortcomings

Reusing the last frames' lane line if a lane line could not be indentified may run into issues if this occurs for consecutive frames.

Problems may occur when changing lanes, once the lane line in the image becomes vertical.

As seen in the "challenge" video, the algorithm is impacted by the camera mounting position, such that peripheral objects now stay in the scene with the constant ROI mask applied. 

The algorithm also runs into trouble when lane lines are not present. Peripheral objects might be identified as lane lines.

Performance seems to be critical as well. On my desktop machine, the videos are barely processed in their runtime. In a car, CPU time and other ressources are typically limited.

###3. Possible improvements

To avoid the risk of not finding a lane for multiple frames, lane finding criteria can be relaxed after not finding a plausible lane line for multiple frames. Failure to identify the lane lines needs to be communicated alongside the output.

Issues with lane lines that appear vertical in the image can be avoided by transforming lane representation to polar.

A possible solution to the mounting position problem would be a learning algorithm to adapt for different mounting positions. Though based on my calibration experience, a better solution would be to use different calibrations for different mounting positions. A learning algorithm runs the risk to do more harm than good, and the camera position is fixed for each car model.

False lane detections may be avoidedable by testing the found lanelines for plausible slopes (possibly provided by a learning algorithm). A Failure to detect lane lines needs to be recognised and communicated to steering/control algorithms.

Performance could be improved be applying the ROI mask first, or cropping the image. The lines produced by the ROI mask could be dealt with by applying a second ROI mask after edge detection.