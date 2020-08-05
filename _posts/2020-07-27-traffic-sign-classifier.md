---
title: "Traffic Sign Classifier"
categories:
  - Blog
tags:
  - Post
---

The purpose of this post is to explain the theory behind the project [Traffic Sign Classifier](https://iacopomc.github.io/projects/2020-07-25-traffic-sign-classifier-project/) and build up some intuition about the process. There won't be any snippets from the source code since we only want to build a high level understanding of why we do what we do.

<br/>

Deep learning has become the most important frontier in both machine learning and autonomous vehicle development. In this project, we'll train a convolutional neural network to classify traffic signs based on the ideas provided in [this paper](http://yann.lecun.com/exdb/publis/pdf/sermanet-ijcnn-11.pdf) by Pierre Sermanet and Yann LeCun.

<br/>

Most of the job is done by the neural network itself, so we'll try to give an explanation of the three biggest steps:
- why do we pre-process the data?
- what do the different layers in the model architecture do?
- how do we tune the hyperparameters?

#### Image pre-processing
It's important to shuffle the training data otherwise its ordering might have a big effect on how well it trains.

#### Model Architecture
Each stage is composed
of a (convolutional) filter bank layer, a non-linear transform
layer, and a spatial feature pooling layer. The spatial pooling
layers lower the spatial resolution of the representation,
thereby making the representation robust to small shifts and geometric distortions

A gradient-based
supervised training procedure updates every single filter in
every filter bank in every layer so as to minimizes a loss
function

#### Hyperparameters

The *epoch* variable just tells *tensorflow* how many times we want it to run our network.

The *batch size* variable tells *tensorflow* how many training images to run through the network at a time. The larger the *batch size*, the faster our model will train but our processor may have a memory limit on how large a batch it can run.

Both hyperparameters *mu* and *sigma* relate to how we initialize our weights.

The *learning rate* tells *tensorflow* how quickly to update the network's weights: *rate = 0.001* is a good default value.

The *Adam Algorithm* minimizes the loss function. It's a little more complicated than *Stochastic Gradient Descent* and it makes a good default choice as an optimizer.
