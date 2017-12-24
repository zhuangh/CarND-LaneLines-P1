# **Finding Lane Lines on the Road** 

[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

<img src="examples/laneLines_thirdPass.jpg" width="480" alt="Combined Image" />


Project 1 Report of Udacity Self-Driving Engineer Nanodegree Term I

Hao Zhuang, hao.zhuang@cs.ucsd.edu
 
## Rubrics
---
### CRITERIA
MEETS SPECIFICATIONS
#### Does the pipeline for line identification take road images from a video as input and return an annotated video stream as output?

The output video is an annotated version of the input video.

#### Has a pipeline been implemented that uses the helper functions and / or other code to roughly identify the left and right lane lines with either line segments or solid lines? (example solution included in the repository output: raw-lines-example.mp4)

In a rough sense, the left and right lane lines are accurately annotated throughout almost all of the video. Annotations can be segmented or solid lines

#### Have detected line segments been filtered / averaged / extrapolated to map out the full extent of the left and right lane boundaries? (example solution included in the repository: P1_example.mp4)

Has a thoughtful reflection on the project been provided in the notebook?

### Reflection 
#### Describes the current pipeline, identifies its potential shortcomings and suggests possible improvements. There is no minimum length. Writing in English is preferred but you may use any language.

## Usage
---
To use the pipeline via docker 

```sh
docker run -it --rm -p 8888:8888 -v `pwd`:/src udacity/carnd-term1-starter-kit P1.ipynb
```

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"
[image2]: ./test_images_output/solidWhiteCurve.jpg "SolidWhiteCurve"
[image3]: ./test_images_output/solidWhiteRight.jpg "solidWhiteRight"
[image4]: ./test_images_output/solidYellowCurve2.jpg "SolidYellowCurve2"
[image5]: ./test_images_output/solidYellowCurve.jpg "SolidYellowCurve"
[image6]: ./test_images_output/solidYellowLeft.jpg "solidYellowLeft"
[image7]: ./test_images_output/whiteCarLaneSwitch.jpg "whiteCarLaneSwitch"
[image10]: ./test_images_output/hsl_challenge.jpg "gray_chanllenge1" 
[image11]: ./test_images_output/gray_challenge.jpg "gray_chanllenge" 
[image12]: ./test_images/challenge.jpg "chanllenge" 
[imageyc_hsl]: ./test_images_output/hsl_solidYellowCurve.jpg "SolidYellowCurve_HSL"
[imageyc_gray]: ./test_images_output/gray_solidYellowCurve.jpg "SolidYellowCurve_Gray"
[imageyc]: ./test_images/solidYellowCurve.jpg "SolidYellowCurve"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of steps as follows.



#### 1. I converted the images to hls and get the color regions of white and yellow to the image mask. 

The reason is that RGB grayscaled image is not robust enough to get the lanes in different light conditions compared to HSL. In the color selection, I set the value ranges for white and yellow. 

#### 2. To get the edges, OpenCV Canny algorithm is used. 

#### 3. Use Hough transformation with the edges to get the lines.

Step 2 and 3 are directly applied from Udacity course so far before the Project 1 assignment. 

Original image of "solidYellowCurve"
![alt text][imageyc] 

Edge detection RGB image
![alt text][imageyc_gray]

From HSL image
![alt text][imageyc_hsl]

The HSL image can detect left lanes well. Another image from the challenge.mp4, 

Original image
![alt text][image12] 
Edge detection RGB image

![alt text][image11]

From HSL image
![alt text][image10]


#### 4. In order to draw a single line on the left and right lanes, I calculate the slope of each line segments. Based on the slope, we separate them into the category of left or right lane. 

For more robust detection, I filter out the too small and too large slopes, weight the lines based on the length, and remove the outliers (outstanding slopes). 

For the weighting lines based on the length, the intuition is the longer the line, the better chance it is the part of lane close to the camera, which is more useful to serve as a baseline of the lane model.

For outstanding slopes, we consider them outside one standard deviation.

"SolidWhiteCurve"
![alt text][image2]

"solidWhiteRight"
![alt text][image3]

"SolidYellowCurve2"
![alt text][image4]

 "SolidYellowCurve"
![alt text][image5]

"solidYellowLeft"
![alt text][image6]

"whiteCarLaneSwitch"
![alt text][image7]

#### 5. Video is a sequence of images. The pipeline memorizes the images in each time step and caches the lines with a fixed size of deque data structure. I use it to weight the lanes to provide a more stable lanes. The assumption is the changes of images are continuous. 

The weight scheme is based on a weighted scheme. For each time step (the index in the deque). The most recent one get larger weight. We use softmax function for this flow. The videos can be accessed via following youtube links

[solidWhiteRight](https://youtu.be/F173u323pXQ)

[solidYellowLeft](https://youtu.be/N5FaqR_urbI)

[challenge](https://youtu.be/v0InM5qh6oM)


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when the road has wide turns. The slopes of the lanes may change ruptly compared to the cached lanes. The weighted or averaging lanes may be unstable.

Another shortcoming could be the steepness of the road. The region of interest assumption in this work may not hold. So it will introduce more unpredicted variables which are not considered in this work.


### 3. Suggest possible improvements to your pipeline


A possible improvement would be to use curvature detection to get more smooth and continuous the line.

For steep roads, we should first need to detect the region of interests.
