
**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)	
[image1]: ./output_images/car1.jpg
[image1b]: ./output_images/notcar1.jpg
[hog]: ./output_images/hog.jpg
[image3]: ./output_images/car_find0.jpg
[image3b]: ./output_images/car_find1.jpg
[image3c]: ./output_images/car_find2.jpg
[image3d]: ./output_images/car_find3.jpg
[image3e]: ./output_images/car_find4.jpg
[image3f]: ./output_images/car_find5.jpg
[image4]: ./output_images/detected3.jpg
[image4b]: ./output_images/detected4.jpg
[image4c]: ./output_images/detected5.jpg
[image4d]: ./output_images/detected6.jpg
[image4e]: ./output_images/detected7.jpg
[image4f]: ./output_images/detected8.jpg
[image4g]: ./output_images/detected9.jpg
[image4h]: ./output_images/detected10.jpg
[image5]: ./examples/bboxes_and_heat.png
[image6]: ./examples/labels_map.png
[image7]: ./examples/output_bboxes.png
[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

###Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the fifth code cell of the IPython notebook called ´main.ipynb´.  

I started by reading in all the `vehicle` and `non-vehicle` images (second cell of code).  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]
![alt text][image1b]

After exploring different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `RGB` color space and HOG parameters of `orientations=11`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:

![alt text][hog]

####2. Explain how you settled on your final choice of HOG parameters.

After trying different combinations of parameters and analysing its results the best combination was chosen to be used in the pipeline.

####3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using sklearn library, using splited random data from the data set (80% training / 20% testing).
It was implemented in the 8th cell of the notebook.

###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

The sliding windows search is done inside the find_car function (11th cell), this function does not consider overlaping. This function is similar to the one suggested by Udacity.
The chosen scales were [1,2], these values were enough for satisfactory car finding algorithm. It receives an image, start/stop y points, scale, orientation, pixels_per_cell, cells_per_block, spatial_size and histogram_bins and return the list of hot_windows.
The area of search were delimited only by y coordination, and was predefined as [350, 600].

####2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?


Ultimately I searched on two scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images of the find_cars_frame function:

![alt text][image3]
![alt text][image3b]
![alt text][image3c]
![alt text][image3d]
![alt text][image3e]
![alt text][image3f]


---

### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./output_10.mp4)


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here are eight frames and their corresponding heatmaps (already considering the previous frames, 'consolidated heat map'):

![alt text][image4]
![alt text][image4b]
![alt text][image4c]
![alt text][image4d]
![alt text][image4e]
![alt text][image4f]
![alt text][image4g]


---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

All of the previous steps were put together to form the pipeline. As a way to avoid false positive, the last 10 frames hot_windows are considered, discarding the oldest ones. 
This procedure was sufficient to avoid a large number of false positives and to present a very satisfactory result.
There is still a problem of detecting cars on the opposite track, but they are quickly disregarded. An improvement for this would be to prevent this region from being part of the search.