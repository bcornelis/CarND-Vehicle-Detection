## Writeup Template
### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./writeup_media/samples_vehicle_non_vehicle.png
[hog_channels]: ./writeup_media/hog_different_channels.png
[hog_orientations]: ./writeup_media/hog_different_orientations.png
[hog_cell_per_block]: ./writeup_media/hog_different_cell_per_block.png
[hog_pixels_per_cell]: ./writeup_media/hog_different_pixels_per_cell.png
[summary_test_img_1.png]: ./writeup_media/summary_test_img_1.png
[summary_test_img_2.png]: ./writeup_media/summary_test_img_2.png
[summary_test_img_3.png]: ./writeup_media/summary_test_img_3.png
[summary_test_img_4.png]: ./writeup_media/summary_test_img_4.png
[summary_test_img_5.png]: ./writeup_media/summary_test_img_5.png
[summary_test_img_6.png]: ./writeup_media/summary_test_img_6.png
[final01]: ./writeup_media/final_01.png
[final02]: ./writeup_media/final_02.png
[finalheat01]: ./writeup_media/final_heat_01.png
[finalheat02]: ./writeup_media/final_heat_02.png
[final_video]: ./final_video_annotated.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the last code cell of the IPython notebook in the method named get_hog_features and it implements the logic as specified in the course.  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `RGB` color space and HOG parameters of `orientations=[4,8,16,32]`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`, `channel=0` :
![alt text][hog_orientations]
For `orientations=[8]`, `pixels_per_cell=(4,8,16,36)` and `cells_per_block=(2, 2)`, `channel=0` :
![alt text][hog_pixels_per_cell]
For `orientations=[8]`, `pixels_per_cell=(4)` and `cells_per_block=[2,4,6,8]`, `channel=0` :
![alt text][hog_cell_per_block]
For `orientations=[8]`, `pixels_per_cell=(8)` and `cells_per_block=(2,2)`, `channel=[0,1,2]` :
![alt text][hog_channels]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and seemingly the following configuration shows the best training score:
* color_space = 'YCrCb'
* orient = 8  
* pix_per_cell = 8
* cell_per_block = 2

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

Using the selected HOG features, all required features are extracted from both the car and the non-car images. The classifier chosen is a linear SVM. This code is available in cell 4 and 5.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

In cell 7 I tried different setups of sliding window searches. The first two are single searches, and the third one is a multi window search. I've included all images so they're a little small, but still the heatmap shows the third methods seems to be the best one. Every line is the analysis of a test image.
![alt][summary_test_img_1.png]
![alt][summary_test_img_2.png]
![alt][summary_test_img_3.png]
![alt][summary_test_img_4.png]
![alt][summary_test_img_5.png]
![alt][summary_test_img_6.png]

![alt text][image3]

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on two scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][final01]
![alt text][final02]
---

### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./final_video.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video. Both of the heat map images correspond to the final images of the previous section:
![alt text][finalheat01]
![alt text][finalheat02]


---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?
* As warned already in the slides, one of the major problems was not to get stuck in the image loading (255 vs 1.0) scale
* every frame there is a full scan (check every window again). It would be possible to apply some smarter scanning (furter away, side cars, ...) to allow for faster results
* also for the final movie, every frame is recalculated. It would be a good idea to smoothen out the results from the previous x frames
  

