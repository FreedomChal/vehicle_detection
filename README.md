# Vehicle Detection and Tracking Project

### Classwork for the [Udacity Self Driving Car Nanodegree](https://www.udacity.com/drive). Here is the [Rubric](https://review.udacity.com/#!/rubrics/513/view) for this project

### Here is the link to my [project code](https://github.com/FreedomChal/vehicle_detection/blob/master/P5.ipynb). A highly detailed description of how every part of my code works can be found there.

### Much of the code I used was from quizzes in the class, primarily HOG classify, Hog Sub-sampling Window Search, and Multiple Detections & False Positives
---

[image1]: ./carsandnoncars.PNG "cars and noncars"
[image2]: ./bboxesfirst.PNG "Regular"
[image3]: ./bboxeslast.PNG "Regular"

## Pipeline

* First, I trained a Support Vector Machine Classifier (SVC) using a Radial Basis Function (RBF) kernel on car and non-car data. For the car and non-car data, the Histogram of Oriented Gradients (HOG) features are extracted using skimage.feature.hog on each color channel, and color histogram and spatial features (low resolution image features) are also extracted. The features are then all concatenated together. Here are examples of a car and noncar training image (regular, features not extracted):

![alt text][image1]

* Later, to find cars in a test image or frame of video, I extract the HOG features of the image, then go through the features, section by section like a sliding window, and for each section, also extract the color histogram and spatial features, then have the SVC make a prediction of the features. Here is an example of boxes drawn over all the patches where the SVC thinks there is a car:

![alt text][image2]

* If the SVC thinks there is a car in the window area patch, add heat in that spot on a heatmap retpresenting where the SVC thinks there are cars. Then, with that heatmap, use thresholding to ignore areas with insufficient heat (not just drawing a box over everywhere where there is any heat for the purpose of removing false positives) in the heatmap on the original image.

![alt text][image3]

* Also, if the pipeline is working with a video, keep a constant heatmap, and when a new heatmap is generated, multiply the constant heatmap by decay hyperparameter (I got that idea from [here](https://github.com/gardenermike/vehicle-tracking/blob/master/vehicle-detection.ipynb)) then add the new heatmap, only with the max value set to 0.5 using np.clip to the constant heatmap.

---

## Discussion

* Perhaps the most major problem I came across was my SVC predicting false positives. I did a lot of hyperparameter tuning, which helped a lot. However, what really made the difference was keeping the persistent heatmap with decay for the video. First I kept a constant heatmap, and at each new frame I did a weighted average of the constant heatmap and the new heatmap, but that tended to lose a lot of data. The new technique works much better.
---
### Hyperparameters
* For the cell_per_block, I used a small number to make the HOG extraction go faster, and it also seemed to just improove the results. 
* For most of the other hyperparameters, I experimented a bit, and settled on their current values, as they worked best. Also, for a couple parameters, I was inspired by the same person I got the idea of using the persistent heatmap with decay.
---
* Using the persistent heatmap with decay was incredibly useful. The reason for it is that when using this method, single time false positives are often ignored. However, cars the SVC consistently predicts are cars are consistently shown as cars, even if the SVC doesn't notice them in a frame or two.
---
* On the project video, my pipeline has issues classifying cars that are on the edge of the image. The main reason for this is that on the video, to deal with false positives, the threshold is fairly high, so that the SVC has to get consistent results for several frames to actualy show an area as a car. While this does get good results for removing false positives, it causes a delay before cars are shown as cars. The need for doing this would be removed if my SVC predicted fewer false positives, so a way to remove this issue would be to train the SVC differently to prevent it from predicting false positives, however I am not sure how to do this. Maybe using a different HOG channel or different spatial size?
