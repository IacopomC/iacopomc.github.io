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

## Model Architecture and Training Strategy

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

<img src="{{ site.url }}/assets/images/behavioral-cloning-project/nvidia_model.png" width="70%"">
