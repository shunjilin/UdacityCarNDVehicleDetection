# Vehicle Detection

Shunji Lin

## Writeup

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[hog]: ./writeup_images/hog.png
[car]: ./writeup_images/car.png
[non_car]: ./writeup_images/non_car.png
[sliding_window_1]: ./writeup_images/sliding_window_1.png
[sliding_window_2]: ./writeup_images/sliding_window_2.png
[sliding_window_3]: ./writeup_images/sliding_window_3.png
[sliding_window_4]: ./writeup_images/sliding_window_4.png
[sliding_window_boxes1]: ./writeup_images/sliding_window_boxes1.png
[sliding_window_boxes2]: ./writeup_images/sliding_window_boxes2.png
[sliding_window_boxes3]: ./writeup_images/sliding_window_boxes3.png
[sliding_window_boxes4]: ./writeup_images/sliding_window_boxes4.png
[sliding_window_boxes5]: ./writeup_images/sliding_window_boxes5.png
[sliding_window_boxes6]: ./writeup_images/sliding_window_boxes6.png


## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for the implementation is [here](./vehicle_detection.ipynb)

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][car]
![alt text][non_car]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=10`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:

![alt text][hog]

#### 2. Explain how you settled on your final choice of HOG parameters.

Given the memory and processing limitations of my machine, I tried to tune the parameters such that I do not get too many features, but at the same time achieve good accuracy during the classification. 10 orientations seem to give a good accuracy on the test set (with the other features). Although I tried to reduce the number of pixels per cell to below 8, it seemed to take too long to extract the features, and produced too many features. I used the default value of 2 for the cells per block parameter.

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using HOG features, together with spatial binning of colors (16 by 16 pixels) and histogam of color channels (16 bins). The images were preprocessed to used the 'YCrCb' color space.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

In implementing a sliding window search, I used different scales to pick up vehicles of different perspective sizes, with a larger scale for vehicles closer to the bottom of the frame and a smaller scale for vehicles closer to the midde of the frame (horizon).
I used scales of 1.2, 1.5, 2.0, 3.0. I used a pixel step of 1 for the overlapping of windows. The sliding windows I used are shown below:

![alt text][sliding_window_1]
![alt_text][sliding_window_2]
![alt_text][sliding_window_3]
![alt_text][sliding_window_4]

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

To consolidate overlapping windows, I used a heatmap approach where I assign a value of +1 for every pixel covered by a positively classified window.

Here are some example images of the sliding windows combined with my classifier, as well as the heatmap visualizations:

![alt_text][sliding_window_boxes1]
![alt_text][sliding_window_boxes2]
![alt_text][sliding_window_boxes3]
![alt_text][sliding_window_boxes4]
![alt_text][sliding_window_boxes5]
![alt_text][sliding_window_boxes6]

Finally by applying a threshold for the pixel values for the heatmap, we can remove classifications for which there is a low number overlapping windows. Finally, by assuming that areas of positive value on the heatmap correspond to vehicle positions, I draw rectangles over these areas to produce the final output image corresponding to the vehicle classifications.

---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video_output.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I used the method described in the previous section to classify the vehicles of each frame of the video. In addition, to remove false positive classifications, I average the heatmap values over 10 frames, and apply a pixel value threshold of 0.5 to remove classifications of low confidence.

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Although the pipeline works reasonably well in detecting vehicles with minimal false positives, certain angles of the white car proved problematic for the pipeline to detect. I believe that is due to the inability of the classifier to generalize to this specific image of the car. One way to solve this might be to train the classifier with more images.

Also, the sliding window technique requires a lot of time to process per frame, and a better method might be to use the state of the art single shot detection methods which bypasses the need for regional detection, and instead works on entire frame images.

===
