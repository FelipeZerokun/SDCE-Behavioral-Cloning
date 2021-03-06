# **Behavioral Cloning** 

---

**Behavioral Cloning Project**
### By Felipe Rojas

The goals / steps of this project are the following:
* Use the simulator to collect data of good driving behavior
* Build, a convolution neural network in Keras that predicts steering angles from images
* Train and validate the model with a training and validation set
* Test that the model successfully drives around track one without leaving the road
* Summarize the results with a written report


[//]: # (Image References)

[image1]: ./template_images/nvidia_cnn_architecture.png "Nvidia Architecture"
[image2]: ./template_images/nn_architecture.png "my CovNet Architecture"
[image3]: ./template_images/center_cam_example.png "Center Cam example"
[image4]: ./template_images/left_cam_example.png "Left Cam example"
[image5]: ./template_images/right_cam_example.png "Right Cam Example"
[image6]: ./template_images/side_to_center_recover_01.jpg "Center Correction 01"
[image7]: ./template_images/side_to_center_recover_03.jpg "Center Correction 03"
[image8]: ./template_images/side_to_center_recover_05.jpg "Center Correction 05"
[image9]: ./template_images/side_to_center_recover_08.jpg "Center Correction 08"
[image10]: ./template_images/side_to_center_recover_10.jpg "Center Correction 10"


[image11]: ./template_images/unflip_right_image.png "Right cam image unflipped"
[image12]: ./template_images/flip_right_image.png "Right cam image flipped"

## Rubric Points
### Here I will consider the [rubric points](https://review.udacity.com/#!/rubrics/432/view) individually and describe how I addressed each point in my implementation.  

---
### Files Submitted & Code Quality

#### 1. Submission includes all required files and can be used to run the simulator in autonomous mode

My project includes the following files:
* model.py containing the script to create and train the model
* drive.py for driving the car in autonomous mode
* model.h5 containing a trained convolution neural network 
* writeup_report.md summarizing the results

#### 2. Submission includes functional code
Using the Udacity provided simulator and my drive.py file, the car can be driven autonomously around the track by executing 
```sh
python drive.py model.h5
```

#### 3. Submission code is usable and readable

The model_creation.py file contains the code for training and saving the convolution neural network. The file shows the pipeline I used for training and validating the model, and it contains comments to explain how the code works.

### Model Architecture and Training Strategy

#### 1. An appropriate model architecture has been employed

My model consists of 5 convolution neural network layer with 3x3 filter sizes and depths between 32 and 64 and activation 'relu' for the non-linearity. (model_creation.py lines 94-98). After the convolutions, I added 4 fully connected layers, beginning with a flatten (model_creation.py line 100) and the layers with a Dropout of 0.5 (model_creation.py lines 101-107).

Before the layers, I added two data preproccesing lines. First, I normalized the data by dividing all the pixels by 255, and to to have a mean of 0, I substracted 0.5 from all the values. All of this with a Lambda function (model_creation.py line 90).
Right after that, I cropped my images to help the model to identify the relevant features within the image. By only having the road on the image, and cropping things like the mountains or the car bonnet, the model may find more relevant features for a better driving pattern. (model_creation.py line 91).

#### 2. Attempts to reduce overfitting in the model

The model contains dropout layers in order to reduce overfitting (model.py lines 102, 104, 106). 

The model was trained and validated on different data sets to ensure that the model was not overfitting (model_creation.py line 53). After I obtained good results (low both on training loss and validation loss), I tested the model.h5 with the Udacity car Simulator to see if the car effectively stayed in the road.

#### 3. Model parameter tuning

The model used an adam optimizer, so the learning rate was not tuned manually (model.py line 25).

#### 4. Appropriate training data

Training data was chosen to keep the vehicle driving on the road. I used a combination of center lane driving, both counter-clockwise and clockwise, recovering from the left and right sides of the road.
As recommended, I did 2 full laps counter-clock wise, 2 full laps clock wise and different recordings on the car recovering from the side of the lanes back to the center.

### Model Architecture and Training Strategy

#### 1. Solution Design Approach

The overall strategy for deriving a model architecture was to try different architectures, going from small ones to bigger ones, to see which one gave me better results. At the beginning, I just added a couple of layers to see how the training went, and then tried different configurations fo the Nvidia architecture.

My first step was to use add some layers to experiments and see how could I reduce the loss values with different parameters. or if I needed better training data. 
After some exploration, I decided to try a convolution neural network model similar to the Nvidia Architecture in the paper "End to End Learning for Self-Driving Cars"  I thought this model might be appropriate because it was build for a similar task with good results.

![alt text][image1]

In order to gauge how well the model was working, I split my image and steering angle data into a training and validation set in a 0.8 / 0.2 ratio. 
Firstly, my model performed poorly in both the training and validation set, so I added more convolutions. After some testing, I used 5 convolution layers, as in the Nvidia architecture and added more epochs to combat underfitting. Then, I used the Nvidia architecture and tested again. This time I found that the training set had low loss, but the validation set was not so good. Here, I added the Dropout layers between the fully connected layers. 
Another approach I could have used is gathering more diverse kind of data, for example, more aggresive side lane to center recovering, or using the other track of the car simulator.

At the end of the process, the vehicle is able to drive autonomously around the track without leaving the road.

#### 2. Final Model Architecture

The final model architecture (model_creation.py lines 98-113) consisted of a convolution neural network with the following layers and layer sizes:
# First, 5 Convolution layers
* Convolution layer with 24 filters, and a filter size of (5,5) with Rectified Linear unit activation ('relu')
* Convolution layer with 36 filters, and a filter size of (5,5) with Rectified Linear unit activation ('relu')
* Convolution layer with 48 filters, and a filter size of (5,5) with Rectified Linear unit activation ('relu')
* Convolution layer with 64 filters, and a filter size of (3,3) with Rectified Linear unit activation ('relu')
* Convolution layer with 64 filters, and a filter size of (3,3) with Rectified Linear unit activation ('relu')
# Four Fully connected layers
* Flatten the output of the last convolution layer
* Dense with 1164 units
* Dropout layer with 50% dropout
* Dense with 100 units
* Dropout layer with 50% dropout
* Dense with 50 units
* Dropout layer with 50% dropout
* Dense with 10 units
* Dropout layer with 50% dropout
* final layer with one output (the steering angle)

Here is a visualization of the architecture (note: visualizing the architecture is optional according to the project rubric)

![alt text][image2]

#### 3. Creation of the Training Set & Training Process

To capture good driving behavior, I used the next approaches:
* Two laps counter-clock wise (the spawn direction of the car)
* Two laps clock wise (turn around the car and drove two laps)
* one lap with side to center correction (small recordings of how the car should behave when going near the edge of the road)
* One lap in the advanced track (small recordings of parts with lots of curves)

Here are examples of the three cameras in the standard track:

![alt text][image3]
![alt text][image4]
![alt text][image5]

I then recorded the vehicle recovering from the left side and right sides of the road back to center so that the vehicle would learn to recover in that kind of situations. Here is one example of the car recovering from the left side of the lane back to the center.

![alt text][image6]
![alt text][image7]
![alt text][image8]
![alt text][image9]
![alt text][image10]

To augment the data sat, I also flipped images and angles thinking that this would help the model learn how to turn to both directions. For example, the previous images show how to correct from the left side of the lane back to center. By flipping the image, I also get an example of how to correct from the right side of the lane back to center. Here is an example of a image, and the flipped image

![alt text][image11]
![alt text][image12]

This process gave me a total of  6783  images for each camera. A total of 20349 images. With the flipping proccess I ended with a dataset size of 40698
I finally randomly shuffled the data set and put 32558 elements into the training set and 8140 into the validation set. (80%/20% rule of thumb)

I used this training data for training the model. The validation set helped determine if the model was over or under fitting. In the beginning I was training the dataset in my computer, but had trouble with the tensorflow. I switch to the Udacity working space and used fewer epochs than before, I ended with 2 epochs, but my initial training was with 5 epochs. I used an adam optimizer so that manually training the learning rate wasn't necessary.
