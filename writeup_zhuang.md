# **Finding Lane Lines on the Road** 

[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

<img src="examples/laneLines_thirdPass.jpg" width="480" alt="Combined Image" />


Project 1 Report of Udacity Self-Driving Engineer Nanodegree Term I

Hao Zhuang, hao.zhuang@cs.ucsd.edu

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

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of steps as follows.

#### 1. I converted the images to hls and get the color regions of white and yellow to the image mask. 


#### 2. To get the edges, I use Canny algorithm. 

#### 3. Use Hough transformation with the edges to get the lines.

#### 4. In order to draw a single line on the left and right lanes, I calculate the slope of each line segments. Based on the slope, we separate them into the category of left or right lane.

![alt text][image2]
![alt text][image3]
![alt text][image4]
![alt text][image5]
![alt text][image6]
![alt text][image7]

#### 5. Video is a sequence of images. The pipeline memorizes the images in each time step and caches the lines with a fixed size of deque data structure. I use it to weight the lanes to provide a more stable lanes. The assumption is the changes of images are continuous. 


The video links are 

[solidWhiteRight](https://www.youtube.com/watch?v=ew_hjAn7kec)

[solidYellowLeft](https://youtu.be/Il7Za6rPZRc)

[challenge](https://youtu.be/8jN73461670)


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when the road has wide turns. The slopes of the lanes may change ruptly.

Another shortcoming could be the steepness of the road. The region of interest assumption in this work may not hold so it will introduce more variables which are not controlled in this work.


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to use curvature detection to get more smooth and continous the line. For steep roads, we should first need to detect the region of interests.