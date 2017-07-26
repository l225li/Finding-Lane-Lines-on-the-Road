# **Finding Lane Lines on the Road**

## Writeup

This writeup file is to document and reflect on the completion of
project 1 of Udacity's Self-driving Car Engineer Nanodegree Program. 

---

**CarND Project 1 - Finding Lane Lines on the Road**

The goals / steps of this project are the following:

* Make a pipeline that finds the lane lines on the road
* Relect on the work

---
## Reflection

### 1. Description of current pipeline

My current pipeline consists of 6 steps. First, I converted the image
to grayscale, then I applied Gaussian blurring (kernel size 5) to the 
gray image. I then used Canny algorithm to detect the edges of of the 
blurred gray image. A mask is applied to the edged image in order to 
only look at edges at the region of interest.

I used Hough Lines algorithm to detect the lines in the edged image 
and draw the lines in red on a new mask. This mask is then added on 
top of the original image and returned as the 'result'. An example 
of the result image:
[image]: ./test_images_output/solidWhiteCurve.jpg "Result"

Instead of the hough line segments, we would like to draw only two 
lines, one for left lane and one for right lane. To accomplish this,
the helper function `draw_lines()` is modified in the following way:

* For each line segments, we calculate the slope and determine whether
the line segment contributes to the left/right lane. Negative slope is
on the left side and positive slope is on the right side (remember in 
the pixel axis, topleft corner is (0, 0)). Lines passed from Hough Lines
are represented by two pairs of points (x1, y1), (x2, y2) where x1 < x2.
Thus, if the slope (y2 - y1) / (x2 - x1) > 0, we conclude that the line 
is on the right side. 
* We collect all the points on the left side into one list `left`, and
all points on the right side into another list `right`
* After we have separated all the points into `left` or `right`, we 
find the max and min pairs for each side by comparing the y coordinates.
For example, (`xmin_left`, `ymin_left`) represents the top point of 
the left lane. 
* Now to extrapolate the lanes, we would tweak `ymax_left` and `ymax_right`
to let them both take the bigger value of them two 
`ymax = max(ymax_left, ymax_right)`. This way we will draw the lane lines
until the longer lane of the two. 
[image]: ./test_images_output/modified_lines.jpg "Result with new `draw_lines()`"


### 2. Potential shortcomings with current pipeline

This pipeline assumes our car driving on perfectly constructed urban 
roads with lane lines marked and in a good weather / road condtion.
One potential shortcoming would be undetectable lane lines when there 
is snow, fog or when it's dark. The visibility of the road would be 
limited and the car would be confused. 

Another shortcoming would be if our car is off the urban road and got
on to routes where there is no well-drawn lane-lines. 

Moreover, the hard-coded region of interest is also concerning. It 
works if our car drives on a reasonably flat road. But what if there
are hills. Then the region of interest would appear differently in the 
picture. 

### 3. Suggestion of possible improvements

One improvement could be to set a more specified threshold for
deciding left/right lane. When we use our current pipeline on the
given challenge video `challenge.mp4`, we can observe that sometimes
instead of drawing the right lane, it draws another lane not far
from the left lane which could be detected by the Canny algorithm
due to the recognizable mark in the middle of the drive lane. Therefore,
we could instead of just checking whether the slope is positive/negative,
experiment and find the approximate slope the left/right lane should be 
and hard code them in. 

However, even if the above suggestion might work on the challenge 
video. This is of course not the most optimized solution - hard coding 
and assuming. We will end up coming across more difficult scenarios,
trying to hard code to tell the computer exactly what to do under each
`if` scenario, and writing millions of lines of code. Using deep learning
to "teach" the car to drive might be a better/more intelligent approach. 
