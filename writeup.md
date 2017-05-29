
**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/car_not_car.png
[image2]: ./output_images/HOG_example.png
[image3]: ./output_images/sliding_windows.png
[image4]: ./output_images/sample_pipeline_image1.png
[image5]: ./output_images/sample_pipeline_image3.png
[image6]: ./output_images/sample_pipeline_image4.png
[image7]: ./output_images/sample_pipeline_image6.png
[image8]: ./output_images/sample_heatmap_image1.png
[image9]: ./output_images/sample_heatmap_image3.png
[image10]: ./output_images/sample_heatmap_image4.png
[image11]: ./output_images/sample_heatmap_image6.png
[video1]: ./project_vehicledetection_3.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  
This is the writup for submission.

###Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the first code cell of the IPython notebook.

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `RGB` color space and HOG parameters of `orientations=8`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image2]

####2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters for orients, pixel per cell, and concluded the default parameters (orients = 9, pixel per cell = (8,8), cells per block = (2,2) is working relatively well. With other parameter combinations, the false positive such as detecting shadow as a car happens more frequently.

####3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using car images and non-car images provided to the project. It is in the section 4 "Training of the classifier". Not only hog features but also spatial features and color histgram features are extracted per image and used for the training of the classifier. For hog features, all of color channels are used to extract features.

###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I implemented a sliding window search per the sample code in the lessons. The area for the search is limited to the areas where vehicle could be observed. Especially in this video, the vehicle is always on the left lane and vehicle would not be detected on the left side, so the search starts with x = 200 as x range.

The window size is (64,64) and overlap fraction is defined as 0.75 between y = 400 and 7 = 528, and window size is (96,96) and overlap fraction is defined as 0.5 between y = 528 and 7 = 720. The area is described as below. This is because the vehicle will be observed as a bigger object when they are close, so no need to have smaller cells in the bottom half of the screen.


![alt text][image3]

####2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

I searched on two scales as described above using RGB 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided results as below.  Here are some example images:

![alt text][image4]
![alt text][image5]
![alt text][image6]
![alt text][image7]
---

### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_vehicledetection_3.mp4)


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here are four video detection results as a result of labeling and their corresponding heatmaps:
![alt text][image8]
![alt text][image9]
![alt text][image10]
![alt text][image11]



---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I can think of two major issues. One is the pipeline still detects shadows as cars. Second is that detected cars are sometimes lost from the detection. 

Previous frame data could be used more effectively to improve the pipeline, other than improving the classifier itself. A car should not suddenly show up or dismiss in the actual world. By using data of previous frames as hints to detect cars, the pipeline should become more robust.

