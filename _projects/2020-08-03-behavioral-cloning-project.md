---
layout: post
title: "Behavioral Cloning"
categories:
  - projects
tags:
  - [Project, Self-Driving, Computer-Vision, Keras, Convolutional-Neural-Networks]
date: 2020-08-03
---

The goal of this project is to use deep neural networks and convolutional neural networks to clone driving behavior.

The steps followed are:
* Use the simulator to collect data of good driving behavior
* Build a convolution neural network in Keras that predicts steering angles from images
* Train and validate the model with a training and validation set
* Test that the model successfully drives around track one without leaving the road

The project is developed using Python and Keras. You can download the full code from [GitHub](https://github.com/IacopomC/CarND-Behavioral-Cloning-P3).

---

### Model Architecture and Training Strategy

My model consists of a convolution neural network inspired by the architecture [published by the Nvidia team]('https://developer.nvidia.com/blog/deep-learning-self-driving-cars/'):

```python
model = Sequential()
model.add(Lambda(lambda x: x / 127.5 - 1., input_shape=(160,320,3)))
model.add(Cropping2D(cropping=((70,25),(0,0))))
model.add(Conv2D(24, (5, 5), activation="relu", strides=(2, 2)))
model.add(Conv2D(36, (5,5), activation='relu', strides=(2, 2)))
model.add(Conv2D(48, (5,5), activation='relu', strides=(2, 2)))
model.add(Conv2D(64, (3,3),activation='relu'))
model.add(Conv2D(64, (3,3),activation='relu'))
model.add(Flatten())
model.add(Dense(100))
model.add(Dropout(0.3))
model.add(Dense(50))
model.add(Dropout(0.3))
model.add(Dense(10))
model.add(Dropout(0.3))
model.add(Dense(1)) # I only want to predict steering angle
```

The model includes RELU layers throughout the convolutional layers to introduce nonlinearity, and the data is normalized in the model using a Keras lambda layer.

Here is a visualization of the architecture of the Nvidia Team:

<br/>

<img src="{{ site.url }}/assets/images/behavioral-cloning-project/nvidia_model.png" width="70%">

---

### Attempts to reduce overfitting in the model

The model contains dropout layers between the final three fully connected layers to combat overfitting:

```python
model.add(Dropout(0.3))
```

The model was trained and validated on different data sets to ensure that the model was not overfitting (code line 10-16). The model was tested by running it through the simulator and ensuring that the vehicle could stay on the track.

---

### Model parameter tuning

The model used an *Adam Optimizer*, so the learning rate was not tuned manually. Since this is a regression network instead of a classification network I used *mean square error* as loss function:

```python
model.compile(loss='mse', optimizer='adam')
```

---

### Creation of the Training Set & Training Process

To capture good driving behavior, I first recorded two laps on track one using center lane driving. Here is an example image of center lane driving:

<br/>

<img src="{{ site.url }}/assets/images/behavioral-cloning-project/center_lane_driving.jpg" width="70%">

<br/>

I then recorded the vehicle recovering from the left side and right side of the road back to center so that the vehicle would learn to steer properly and get back on track. A little trick here is to delete the data entries where the steering value is zero, because it contains the action of driving the car along the sideline.

These images show what a recovery looks like:

<br/>

<img src="{{ site.url }}/assets/images/behavioral-cloning-project/recovery_01.jpg" width="32%">
<img src="{{ site.url }}/assets/images/behavioral-cloning-project/recovery_02.jpg" width="32%">
<img src="{{ site.url }}/assets/images/behavioral-cloning-project/recovery_03.jpg" width="32%">

<br/>

To help the model generalize better I recorded the vehicle driving counter-clockwise

Data augmentation can help generate more points in the "feature" space and make the trained model more robust. To augment the data set, I also flipped the images horizontally and inverted the angles this would teach the car to steer clockwise and counter-clockwise.

I used the side car images for training too. This carries two benefits::
* it's three times as much data
* using these images will teach the vehicle to steer towards the center if it starts drifting off towards the sides

Since the steering angle associated to the side cameras is the same as the one in the center even though the image appears off to one side of the road, I applied a correction factor of *+- 0.2* to the relative steering angle.

After the collection process, I had X number of data points. I then preprocessed this data by removing the top 70 pixels that contains the landscape and the bottom 25 pixels that contains the car hood.

In order to gauge how well the model was working, I split my image and steering angle data into a training and validation set with an 80/20 ratio.

I used this training data for training the model. The validation set helped determine if the model was over or under fitting. The ideal number of epochs was 2 as evidenced by the graph below:
