# DLforAI_RCNNs
Common repository for Spr2019 Deep Learning for Autonomous Vehicles Traffic Light and NEXET Assignments

## Credit Where Credit is Due
This assignment would not have been possible without two resources:

### 1. Faster R-CNN (object detection) implemented by Keras for custom data from Google’s Open Images Dataset V4
* Author: Yinghan Xu
* Date: 11/20/2018
* URL: https://towardsdatascience.com/faster-r-cnn-object-detection-implemented-by-keras-for-custom-data-from-googles-open-images-125f62b9141a
* Git: https://github.com/RockyXu66/Faster_RCNN_for_Open_Images_Dataset_Keras

Given that our class material only covered simple CNNs up until this past Monday, I had no idea where to start for a more complex problem that involved object recognition within a larger image. Xu's article was probably the 3rd or 4th online example I attempted to implement and was the first I managed to have successfully get to the training portion of the project. The vast amount of my effort for these assignments went into data preprocessing, analyzing Xu's article, adapting it to the datasets, and debugging / tweaking / working to understand the code.

### 2. Google Colab
Although I have a GTX1060 6GB graphics card in my personal rig, I discovered that I was not able to load the VGG-16-based model and any respectable number of training images. Thanks to cheap storage options from Google Drive ($3 for 100GB per month is incredible!), a healthy 5 GHz WiFi conenction, and saving weights, I was able to train and evaluate models for both the traffic lights and NEXET datasets.

## Files
This is a common repository for two assignments. All files related to a certain assignment are found within that subfolder. The images subfolder for each subfolder is used to host images used in this readme.

The saved weights of each model (files listed below) are not included due to GitHub size limiatations:
* nexet
  * nexet_model_frcnn_vgg.hdf5: 534MB, available on request via Google Drive
  * vgg16_weights_tf_dim_ordering_tf_kernels.h5: 540MB, available from https://github.com/fchollet/deep-learning-models/releases/download/v0.1/vgg16_weights_tf_dim_ordering_tf_kernels.h5
* traffic lights
  * large_anchors_model_frcnn_vgg.hdf5: 535MB, available on request via Google Drive
  * model_frcnn_vgg.hdf5: 535MB, available on request via Google Drive
  * vgg16_weights_tf_dim_ordering_tf_kernels.h5: 540MB, available from https://github.com/fchollet/deep-learning-models/releases/download/v0.1/vgg16_weights_tf_dim_ordering_tf_kernels.h5

## Traffic Lights
The traffic lights dataset was the first assignment I started with. Unfortunately, it is also the dataset for which I got the worst results.

### Preprocessing
The traffic lights dataset required the most preprocessing because the data was delivered in the .yaml format, not a traditional CSV. I modified the bosch sample script from their github to translate the yamls for test and training into CSVs (Sullivan_M2_SubProj1_TrafficLights_ProcessYaml.ipynb). There are several output files (riib, rgb, colab...) as I was experimenting with running on the local machine and then on Colab. I changed from using the riibs to rgb images for easier debugging.

### Adjustments from RCNN Source Code
With the images and training csv uploaded to google drive, I started modifying the source notebook to train the data. The data parsing function needed to be updated for my csv format. As I was targeting quick training speed, I lowered the number of batches per epoch from 1000 to 500 and turned off augmentations like flips and rotations. I also updated the RPN debugging section to describe all the ground truth boxes for the image (green). After training for a few days with the stock code and getting no test results, I restarted the training with smaller anchor sizes. The image below shows the RPN debugging section with the small anchor sizes.

![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/traffic_lights/images/tlights_RPNDebug.png)

Finally, I combined the test and training notebooks from the source into a common file. I experimented with having a "mode" variable to switch between training and testing, but found i didn't like erasing the outputs of my training code blocks everytime I wanted to test.

### Results and Discussion
The results are mixed. Over the 40 epochs trained for the smaller anchor size, the training seems to progress favorably. The class accuracy of each ground truth box improves, the RPN model losses improve with each epoch, as do the losses for the classification model. This model took ~20 minutes to train on 500 images, and ~40 minutes to load the data into the Google GPU. Given the session limit of 12 hours, I was usually able to train about 20 epochs per 12 hour period, at which point I'd have to reconnect and continue the training.

![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/traffic_lights/images/tlights_loss1.png)
![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/traffic_lights/images/tlights_loss2.png)

The results of the training were poor. When tested, the model would not predict any traffic lights, let alone classify them (as seen in the example image below with the traffic light in the upper right corner).

![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/traffic_lights/images/tlights_testex1.png)

At this point, I had not begun experimenting with the NEXET dataset. I tried decreasing the threshold for the predictions and decreasing the anchor size, but I didn't have any luck. I suspect that the traffic lights are too small in the image for the base settings. The source code resizes the image from 1280 x 720 to 530 x 300 for training speed improvements, and the stride of the RPN model is set to 16, meaning that even with my smaller anchor size, I was likely missing bounding boxes. I think this is one of the reasons my average overlapping boxes value was always lower than 1. 

### Next Steps
For next steps, I would start by increasing the image size fed to the model (by decreasing the image reduction) to see if that helped improve the detection. If so, I would also experiment with changing the stride of the model and the downstream dimensional changes to accomodate that. Finally, to save training time, I would experiment with cropping the bottom third of the image.

## NEXET
I "forked" my work with the traffic lights dataset to the NEXET dataset (or rather, a portion of it) and had much better luck. I suspect this is because the objects of interest make up a larger portion of the image than traffic lights.

### Preprocessing
As NEXET delivered the training data in a csv file, there was little preprocessing required. I did however use a local notebook script to truncate the training csv to only include information for the ~3700 image files I uploaded to Google Drive rather than the entire training dataset. I struggled trying to update a pandas dataframe line by line with a while loop until I realized it supports SQL-like merge commands. 

### Adjustments from RCNN Source Code
This code maintained all of the improvements I had performed for the traffic lights dataset. I did restore horizontal filps in the data augmentation, and I put the anchor sizes back to those of the original author as I was detecting larger portions of the image now. Additionally, as I ended up actually getting predicitions out of this model, I added some code to generate a predicition for each test image I uploaded and store it in a csv (test_output.csv). Note, this is not the entire test dataset, but only a few thousand images. 

The image below shows the output of training image from the RPN debugging portion of the notebook. It does a good job of demonstrating the difficulties of having only a few anchor sizes. 
![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/nexet/images/nexet_RPNDebug.png)

Note that the screen captures for the NEXET dataset are from the dark mode Colab notebook, hence the dark background, while the traffic dataset images were from the Github preview. Since I printed out a lot of test image results in my NEXET notebook, you won't be able to preview it on Github.

### Results and Discussion
Again, the model seemed to train very well. It also trained much more quickly per epoch than the traffic lights dataset, likely because the model was actually finding regions to propose to the classification network. 
![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/nexet/images/nexet_loss1.png)
![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/nexet/images/nexet_loss2.png)
![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/nexet/images/nexet_loss3.png)

In the following sections, I present a few of the images I printed during the notebook's testing portion. The predictions for every test image I uploaded to Google drive can be found in test_output.csv.

#### Success
These images show relatively successful test images. The first image shows a very favorable scene where all traffic is traveling in the same direction and there are no buildings in the center of the camera. The second one shows a common phenomenon of many IDs being grouped at the middle height of the camera (i.e. the horizon). Finally, the third image is interesting when compared to the blurry night image of the Failures section. I think this image did OK because the blurring is a bloom effect and does not distort the general image of the car too much, where as the corresponding failure has long vertical blurs.

Well lit city scene, no oncoming traffic:
![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/nexet/images/nexet_testex5.png)

Night driving, cars well illuminated, no blurring:
![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/nexet/images/nexet_testex1.png)

Night driving with blurring, but not long vertical blurs like below:
![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/nexet/images/nexet_testex4.png)

#### Failure Cases
I personally find the failures more interesting than the successes. The first image here corresponds with the successful night image from above. The second image seems to be a clear case of the camera being out of focus and thus the cars lacking disting outlines. It's also during the day so there are no collaocated bright spots like head or taillights for the model to react to. Finally the third image demonstrates how background clutter and building edges can mimic a truck or bus very close to the camera. 

Night driving with rain, car outlines and lights altered into long vertical blurs:
![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/nexet/images/nexet_testex2.png)

Sunny day, but camera out of focus:
![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/nexet/images/nexet_testex3.png)

Well lit day city scene, but mix of light colored buildings and oncoming cars:
![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/nexet/images/nexet_testex6.png)

### Next Steps
The easiest thing that would likely improve the models performance would be to upload and feed it much more of the training dataset. Additionally, I would like to experiment with vertical translations of the data to avoid the model getting too used to cars in the middle of the image (i.e. the horizon). Finally, I'd like to increase the range of anchor sizes and tighten up the acceptable RPN tolerance to get better bounding box accuracy.
