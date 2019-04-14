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

### Results
The results are mixed. Over the 40 epochs trained for the smaller anchor size, the training seems to progress favorably. The class accuracy of each
![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/traffic_lights/images/tlights_loss1.png)
![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/traffic_lights/images/tlights_loss2.png)
![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/traffic_lights/images/tlights_testex1.png)

### Discussion and Next Steps




## NEXET

### Preprocessing

### Adjustments from RCNN Source Code

### Results

### Discussion and Next Steps



### RPN Debugging
![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/nexet/images/nexet_RPNDebug.png)
![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/nexet/images/nexet_loss1.png)
![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/nexet/images/nexet_loss2.png)
![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/nexet/images/nexet_loss3.png)
![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/nexet/images/nexet_testex1.png)
![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/nexet/images/nexet_testex2.png)
![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/nexet/images/nexet_testex3.png)
![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/nexet/images/nexet_testex4.png)
![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/nexet/images/nexet_testex5.png)
![alt text](https://github.com/d717an/DLforAI_RCNNs/blob/master/nexet/images/nexet_testex6.png)

