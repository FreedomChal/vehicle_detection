# Vehicle Detection and Tracking Project

### Classwork for the [Udacity Self Driving Car Nanodegree](https://www.udacity.com/drive). Here is the [Rubric](https://review.udacity.com/#!/rubrics/513/view) for this project

### Here is the link to my [project code](https://github.com/FreedomChal/vehicle_detection/blob/master/P5.ipynb). A highly detailed description of how every part of my code works can be found there.

### Much of the code I used was from quizzes in the class, primarily HOG classify, Hog Sub-sampling Window Search, and Multiple Detections & False Positives
---

[image1]: ./carsandnoncars.PNG "cars and noncars"
[image2]: ./bboxesfirst.PNG "Regular"
[image3]: ./bboxeslast.PNG "Regular"

## Pipeline

* First, I trained an SVC on car and noncar data similar to these, with the HOG features extracted:

![alt text][image1]

* Then, to find cars, a sliding window goes across the image, and for the area where the window is, have the SVC predict if there is a car there or not. Here is an example of boxes drawn over all the patches where the SVC thinks there is a car:

![alt text][image2]

* If the SVC thinks there is, add heat in that spot on a heatmap retpresenting where the SVC thinks there are cars. Then, with that heatmap, draw a box over where areas with sufficient heat (not just drawing a box over everywhere where there is any heat for the purpose of removing false positives) in the heatmap on the original image.

![alt text][image3]

* Also, if the pipeline is working with a video, keep a constant heatmap, and when a new heatmap is generated, take a weighted mean of the constant heatmap and the new heatmap, and use that for drawing the boxes, then set the constant heatmap to the weighted average heatmap.

---

## Discussion

* One of the main problems I came across was that in a certain section of the video, the SVC mistakenly classifies the concrete guardrail as a car. To try to deal with this, to make the SVC less likely to classify images as cars I had it train on all the noncars in the dataset, but thousands fewer cars. This did not fully fix the problem, but greatly reduced it.
---
* For the HOG paramaters, the reason I used the paramaters I did was because, for the cell_per_block, I used a small number to make the HOG extraction go faster. For all the others, I just experimented a little and found the paramaters I settled on worked well.
---
* On the project video, my pipeline has issues classifying cars that are further away, and cars that are extremely close. This could be perhaps be solved by decreasing the ystart, increasing the ystop, and by getting more data to train on, maybe the optional Udacity dataset.
