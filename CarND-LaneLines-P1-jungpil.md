# Finding Lane Lines on the Road

### This writeup for this project was written as a partial fillfulment of the requirements for the Nono degree of "Self-driving car Engineer" at the Udacity.

---
### The goals of this project are the following:
#### - Make a pipeline that finds lane lines on the road
#### - Reflect on the work in a written report

[//]: # (Image References)

[image1]: ./examples/line-segments-example.jpg
[image2]: ./test_images_output/solidWhiteRight_segment.jpg
[image3]: ./test_images_output/solidWhiteRight.jpg
[image4]: ./examples/P1_example.mp4
[image5]: ./test_videos_out/solidWhiteRight.mp4
[image6]: ./test_videos_out/challenge.mp4


---

### Reflection

### 1. Description of the pipeline

The pipeline consists of 6 steps which are sumarized in the below. 
1. Read an image and convert it to the grayscaled image. (`grayscale()`)
2. Process it with a Gaussian filter for blurring and smoothing effects. (`gaussian_blur()`)
3. Apply the Canny algorithm to detect edges in the image. (`canny()`)
4. Mask edges out of the region of interest. (`region_of_interest()`)
5. Apply the Hough transform algorithm to get segments of lane line candidates. (`hough_lines()`)
6. Process the segment and draw lane lines in the image. (`draw_lines()` called from `hough_lines()`)

All steps have been well described in the course material. In the project kit, the helper functions were given and all the tasks were completed with the help of them. The used helper functions are denoted in () at the end of each description in the above.

The end result of the pipeline is shown in the image below.
![Example given][image2]
For the purpose of comparison, the processed image given in the kit is also shown in the below.
They are almost the same.
![Example given][image1]

### 2. Description of the `draw_lines()` function modification
In order to draw a single line on the left and right lanes, the `draw_lines()` function was modified.
The algorithm has two steps which is detailed as follows.
the first step is getting the most upper point and its slope for the given image by
1. Extracting the $(x_1, y_1), (x_2, y_2) $ points from both ends of all edges by Hough algorithm.
2. Computing the slope by $(x_2-x_1)/(y_2-y_1)$ and decide left or right lane points.
 - the slope of left lane points are between two negative values
 - whereas that of right lane points are between two positive values.
 - The exact positive and negative values are the parameters to be decided.
3. Getting fitting line slope and the most upper point.
 - The openCV library function `fitLine()` was called to get the slope.
 - The most upper point was obtained by looking for the point of smallest $y$ value.
 
In this way, the pair of the most upper point and the slope are obtained for each image

The second part is averaging the most upper point position and slope vales in the past $N$ pair of point and slope.
The window size $N$ is to be decided as a tuning parameter.
Finally, the line is delineated by extrapolating from the averaged upper points to the image bottom boundary with the averaged slope.
Actually, in case of a still image, the second part of the algorithm is not necessary because there is no past images. However, that renders robust lane lines in case of video streams.

This `draw_lines()` function modification results in the image below when appled to the above example. The two straight lane lines are certainly indentified.
![Example given][image3]

The other all processed images are found in [the attached html file](file://CarND-LaneLines-P1-jungpil.html)
As a video stream example, the below video shows the result with [challenge.mp4 file](file://test_videos_output/challenge.mp4)

![Example given][image6]

The other all processed video are found in [the attached html file](file://CarND-LaneLines-P1-jungpil.html)

### 3. Potential shortcomings and possible improvements suggestion
Several points can be cited as potential shortcomings.
First of all, all the parameters are manually tuned, so that they cannot be confirmed as optimal values for other untested images and videos. This weak point could be mitigated by introducing a paramter-self-tuning algorithm.
Secondly, the lane cannot be always the stright line. The curvature characteristics could be introduced for the improvement.

Another potential shortcomming but not the last is as follows.
This algorithm could fail to function, when cars in front persistently shade the lane line.
In such a case, just edge detection-like methods won't work correctly. Possibly, the other information sources should be supported such as a map and a localization.