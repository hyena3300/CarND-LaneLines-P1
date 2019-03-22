# **Finding Lane Lines on the Road** 
---

**Objectives**
* Make a pipeline that finds lane lines on the road.
* Trace out the lane lines onto existing images and videos
* Reflect about the shortcomings and possible improvements.


[//]: # (Image References)
[original]: ./writeup_images/original.jpg "Original"
[greyscale]: ./writeup_images/grayscale.jpg "Grayscale"
[canny]: ./writeup_images/canny.jpg "Canny"
[canny_masked]: ./writeup_images/canny_masked.jpg "Canny Masked"
[hough_transform]: ./writeup_images/hough_transform.jpg "Hough Function Result"
[guide_lines]: ./writeup_images/guide_lines.jpg "Guide Lines"
[target_output]: ./writeup_images/target_output.jpg "Target Result"

---

## Original Image
![alt text][original]

## Target Image
![alt text][target_output]

## Pipeline
### The pipeline consisted of 7 steps.

* First, I converted the images to grayscale so that we can perform edge detection. The reason why we use greyscale is because it can show better contrast than in normal colors.
![alt text][greyscale]

* After converting to greyscale, we apply Gaussian blur to each of the greyscale images. This will help suppress the noise in the image.

* Then we apply Canny edge dectection to find the edges in the image. This proces takes the blurred greyscale images and creats a gradient image. A brighter pixel value means that it is more likely an edge. We use a threshold here to help us narrow down stronger edges.
![alt text][canny]

* The Canny edge detection helps make edges more prominent to the human eyes. Computers still only sees pixels. In order to help computers to draw lines, we need to help define what is a straight line. But first, we should just focus on the area of interest. We create a mask of the area that we are interested in.
![alt text][canny_masked]

*  We now turn to the Hough function. Hough function uses the function (rho = x * cos theta + y * sin theta) to map each of the pixels between -90 degrees to 90 degrees. The result is a distance of the pixel from the origin of the polar coordinate. The more pixels that has the same distance it means they are more likely to exist on the same straight line. We call this a vote. For the Hough function we define the a threshold of minimum of votes needed before we try to make a line at an intersection. After collecting the intersection, it will create lines segments. These segments must also meet a minimum length before being considered. There is also a max line gap where it is a distance in pixel where two line segments can still be considered as one line. The result are line segments that we can draw out.
![alt text][hough_transform]

* This should look similar to the white lines that we saw from the Canny masked image. It will be much easier to read if we drew two guide lines instead. Since Hough function returns line segments, I first have to classify if the line segment belongs to the left or right lane. Using the slope of the of the segments, the positive ones belong to the right line and the negative ones belong to the left lane. During the classification, I try to find the minimum and maximum points of lane segments. This produced two lanes but they were still different lengths. The last thing that I had to do is to find points on the current lanes so that both lanes' length matches.
![alt text][guide_lines]

* The last step is to overlay the computed lanes onto the original image which gives us the target output.
![alt text][target_output]


## Reflection
### Potential shortcomings with your current pipeline

* Only works for straight lines.
* Only works if the lanes are within the masked area.
* There are possible division by zero errors if both left and right lanes aren't in the masked area.
* This only works in well lit conditions and both the lanes are prominate on the road.
* Possible misclassification of lane segments. Right lane segments were classified as part of the left lane. Also, perpendicular line segments are considered to be part of a lane.

### Improvements to your pipeline

* Improve the Hough transform to support curving lanes.
* Better lane classification is needed. Just using the slope of line segments can give false positive result. Can use the mask rectangle to help with classification.
