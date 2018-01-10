## CarND-Vehicle-Detection

---

**Vehicle Detection Project**
---

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally,  Apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Implement a sliding-window technique and use the trained classifier to search for vehicles in images.
* Run the pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/CarAndNonCarImageFromDataset.png
[image2]: ./output_images/HogVisualization.png
[image3]: ./output_images/FindCarFunctionOutputOnPipeline.png
[image4]: ./output_images/PipelineWithHeatMapandfinalOutput.png
[image5]: ./output_images/
[image6]: ./output_images/
[video1]: ./project_video_output.mp4


---

### Histogram of Oriented Gradients (HOG)

#### 1. HOG feature extraction

The code for this step is contained in the code cell 4 and 5 of the IPython notebook 

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image2]

#### 2. Choice of HOG parameters

The training images are of size (64,64). It made sense to use (8x8) pixels per cell. I tried different orientations like 6 and 9 and both of them gave reasonable results and the alignment of gradients in hog visualization was able to clearly differentiate between car and non -cars .So, I finally decided to go wih orientation value = 9

#### 3. Training  classifier 

The code for training classifier is provided in code cell (6-10) . 
In code cell 6, features are extracted from training data using `extract_features` which uses hog features , binned color features and  histogram of all color channelst to compute a 1-D feature array for a training image.
In code cell 7, data is normalized using `StandardScaler()` and then data is divided into training (85%) and testing data (15%) .
In code cell 8, linear SVM was used to train the data . It took about 15.85 seconds to train SVC for feature vector length of 8460. After that, trained classifier is tried on test data to compute accuracy. 
I played with different parameters like `cspace` , `hog_channel` .  My observations are given below-

Pameter Tuning Observations
With cspace = 'RGB' and hog_channel = 0, Test_Accuracy = 97.22 %, Feacture vector length = 4932 
With cspace = 'YCrCb' and hog channel = ALL, Test_Accuracy = 97.78 %, Feacture vector length = 4932
With cspace = 'RGB' and hog_channel = 0, Test_Accuracy = 98.06 %, Feacture vector length = 8460 
With cspace = 'YCrCb' and hog channel = ALL, Test_Accuracy = 99.72 %, Feacture vector length = 8460

I finally settled on `cspace =  YCrCb ` and `hog channel =ALL` and it gave me accuracy = 99.72%


### Sliding Window Search

#### 1.  Sliding window search Implementation

I used `find_cars` function (code cell 10) which first extracted all the hog features from an image and then subsampled the image to divide it into different overlaying windows for which the trained classifer i.e `svc` was used to predict whether the car existed in a window and if vehicle is detected ,then it output an image with a boxes drawn over the vehicle. `scale` parameter was used to select the size of sliding  window and `cell per step` defined the overlap between windows. I used `cell per step = 2` which is equivalent to window overalap of 75%. I used this method as this is faster than method which  first finds windows to search using siding window and then computes hog features from each search window. 


#### 2. Example test images

Ultimately I used YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image3]
---

### Video Implementation

#### 1. Final Video Output
Here's a [link to my video result](./project_video_output.mp4)


#### 2 Heat Map and Combining overlapping bounding boxes over vehicles

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.

Here's an example result showing the heatmap from a series of frames of video, and the final output with the bounding boxes then overlaid on the frames of video .

![alt text][image4]

---

### Discussion

####  Challenges and Potential Improvements

I faced issues with false positives during detection. I improved my classifier by tuning parameters to take care of false positives . I could definitley make it more robust by using previous video frames and keep track of detected vehicles using  averaging technique on previous frames.  That would help me to avoid false positives. My pipeline can potentailly fail for objects that it was not trained for like a pedestrian, bicycle etc. 

