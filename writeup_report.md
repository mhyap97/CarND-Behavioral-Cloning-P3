# **Behavioral Cloning** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Behavioral Cloning Project**

The goals / steps of this project are the following:
* Use the simulator to collect data of good driving behavior
* Build, a convolution neural network in Keras that predicts steering angles from images
* Train and validate the model with a training and validation set
* Test that the model successfully drives around track one without leaving the road
* Summarize the results with a written report

[Track 1 video on Youtube](https://youtu.be/dTyNoMT9vRE)

[Track 2 video on Youtube](https://youtu.be/CSO_ZDmWWk4)

[//]: # (Image References)

[image1]: ./examples/figure2.png "Model Visualization"
[image2]: ./examples/figure3.jpg "Center Lane Driving"
[image3]: ./examples/figure4.jpg "Recovery Image"
[image4]: ./examples/figure5.jpg "Recovery Image"
[image5]: ./examples/figure6.jpg "Recovery Image"
[image6]: ./examples/figure7.jpg "Normal Image"
[image7]: ./examples/figure8.jpg "Flipped Image"

## Rubric Points
### Here I will consider the [rubric points](https://review.udacity.com/#!/rubrics/432/view) individually and describe how I addressed each point in my implementation.  

---
### Files Submitted & Code Quality

#### 1. Submission includes all required files and can be used to run the simulator in autonomous mode

My project includes the following files:
* model.py containing the script to create and train the model
* drive.py for driving the car in autonomous mode
* model.h5 containing a trained convolution neural network 
* writeup_report.md or writeup_report.pdf summarizing the results

#### 2. Submission includes functional code
Using the Udacity provided simulator and my drive.py file, the car can be driven autonomously around the track by executing 
```sh
python drive.py model.h5
```

#### 3. Submission code is usable and readable

The model.py file contains the code for training and saving the convolution neural network. The file shows the pipeline I used for training and validating the model, and it contains comments to explain how the code works.

### Model Architecture and Training Strategy

#### 1. An appropriate model architecture has been employed

I decided to use [nVidia Autonomous Car Group model](https://devblogs.nvidia.com/parallelforall/deep-learning-self-driving-cars/) with 5x5 & 3x3 convolution neural network and depths between 24 and 64 (model.py lines 110-119) 

The model includes RELU layers to introduce nonlinearity (code line 110), and the data is normalized in the model using a Keras lambda layer (code line 103). 

Here's a summary of my model:
```
Total Images: 34308
Train samples: 27446
Validation samples: 6862
_________________________________________________________________
Layer (type)                 Output Shape              Param #
=================================================================
lambda_1 (Lambda)            (None, 160, 320, 3)       0
_________________________________________________________________
cropping2d_1 (Cropping2D)    (None, 90, 320, 3)        0
_________________________________________________________________
conv2d_1 (Conv2D)            (None, 43, 158, 24)       1824
_________________________________________________________________
conv2d_2 (Conv2D)            (None, 20, 77, 36)        21636
_________________________________________________________________
conv2d_3 (Conv2D)            (None, 8, 37, 48)         43248
_________________________________________________________________
conv2d_4 (Conv2D)            (None, 6, 35, 64)         27712
_________________________________________________________________
conv2d_5 (Conv2D)            (None, 4, 33, 64)         36928
_________________________________________________________________
flatten_1 (Flatten)          (None, 8448)              0
_________________________________________________________________
dense_1 (Dense)              (None, 100)               844900
_________________________________________________________________
dense_2 (Dense)              (None, 50)                5050
_________________________________________________________________
dense_3 (Dense)              (None, 10)                510
_________________________________________________________________
dense_4 (Dense)              (None, 1)                 11
=================================================================

Total params: 981,819
Trainable params: 981,819
Non-trainable params: 0
```

#### 2. Attempts to reduce overfitting in the model

I did not use regularization techniques like Dropout or Max pooling for my model. Instead, I used low epochs approach which is 3 epochs. Then, I split my sample data into training and validation data. Using 80% as training and 20% as validation (model.py line 130-132).

#### 3. Model parameter tuning

The model used an adam optimizer, so the learning rate was not tuned manually (model.py line 144).

#### 4. Appropriate training data

Training data was chosen to keep the vehicle driving on the road. I used a combination of center lane driving, recovering from the left and right sides of the road ... 

For details about how I created the training data, see the next section. 

### Model Architecture and Training Strategy

#### 1. Solution Design Approach
At first, I tried with a same model of nVidia Autonomous Car Group but applied regularization techniques like Dropout and using elu as activation, with a 6 training epochs.
But then I found out another approach to reduce the training duration with almost the same result when trying the autonomous mode in the simulator, which is the final model I'm using now.
I removed the Dropout layers and with a lower epochs, the training duration reduced.
If I increase the epochs to more than 3 epochs, it seems to be overfitting, the validation loss will start to increase after 3 epochs.

#### 2. Final Model Architecture

The final model architecture (model.py lines 110-119) 
Here is a visualization of the architecture ( Picture from [nVidia Autonomous Car Group model](https://devblogs.nvidia.com/parallelforall/deep-learning-self-driving-cars/) ):

![alt text][image1]

#### 3. Creation of the Training Set & Training Process

To capture good driving behavior, I first recorded 1 lap on both track one and two using center lane driving. Here is an example image of center lane driving:

![alt text][image2]

I then recorded the vehicle recovering from the left side and right sides of the road back to center so that the vehicle would learn to drive back to center of the road when when it’s off on the side of the road. These images show what a recovery looks like:

![alt text][image3]
![alt text][image4]
![alt text][image5]

Then I repeated this process on track two in order to get more data points.

To augment the data sat, I also flipped images and angles thinking that this would helping with the left turn bias involves flipping images and taking the opposite sign of the steering measurement. For example, here is an image that has then been flipped:

![alt text][image6]
![alt text][image7]

I also turn the car around and record counter-clockwise laps around the track to combat the bias. Driving counter-clockwise is also like giving the model a new track to learn from, so the model will generalize better.

After the collection process, I had 34308 Total Images, 27446 Train samples, and 6862 Validation samples.

I finally randomly shuffled the data set and put 20% of the data into a validation set. 

I used this training data for training the model. The validation set helped determine if the model was over or under fitting. The ideal number of epochs was 3 as evidenced by the validation loss increased after epoch 3. I used an adam optimizer so that manually training the learning rate wasn't necessary.
