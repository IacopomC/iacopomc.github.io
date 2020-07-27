---
layout: post
title: "Traffic Sign Classifier"
categories:
  - projects
tags:
  - Project
date: 2020-07-25
---

The goals of this project is to design and implement a deep learning model that learns to recognize traffic signs. The dataset used is the [German Traffic Sign Dataset](http://benchmark.ini.rub.de/?section=gtsrb&subsection=dataset) .  
The starting point is the LeNet-5 implementation, the only changes are the number of classes and the preprocessing.

The steps followed are:

* Load the data set (see above for links to the project data set)
* Explore, summarize and visualize the data set
* Design, train and test a model architecture
* Use the model to make predictions on new images
* Analyze the softmax probabilities of the new images

The project is developed using Python and OpenCv. You can download the full code from [GitHub](https://github.com/IacopomC/CarND-Traffic-Sign-Classifier-Project).

---

### Load data set

I used a pickled dataset in which the images have already been resized to 32x32. It contains a training, validation and test set.

```python
training_file = './train.p'
validation_file= './valid.p'
testing_file = './test.p'

with open(training_file, mode='rb') as f:
    train = pickle.load(f)
with open(validation_file, mode='rb') as f:
    valid = pickle.load(f)
with open(testing_file, mode='rb') as f:
    test = pickle.load(f)

X_train, y_train = train['features'], train['labels']
X_valid, y_valid = valid['features'], valid['labels']
X_test, y_test = test['features'], test['labels']
```

The pickled data is a dictionary with 4 key/value pairs:

- *features* is a 4D array containing raw pixel data of the traffic sign images, (num examples, width, height, channels).
- *labels* is a 1D array containing the label/class id of the traffic sign.
- *sizes* is a list containing tuples, (width, height) representing the original width and height the image.
- *coords* is a list containing tuples, (x1, y1, x2, y2) representing coordinates of a bounding box around the sign in the image.

### Data Set Summary & Exploration

I used the numpy library to calculate summary statistics of the traffic signs data set:

* The size of training set is **34799** samples
* The size of the validation set is **4410** samples
* The size of test set is **12630** samples
* The shape of a traffic sign image is **(32, 32, 3)**
* The number of unique classes/labels in the data set is **43**

Here is an exploratory visualization of the data set. They are bar charts showing the data distribution for training, validation and test set is the same.

<br/>

<img src="{{ site.url }}/assets/images/traffic-sign-classifier-project/train_classes_distribution.png" width="32%">
<img src="{{ site.url }}/assets/images/traffic-sign-classifier-project/validation_classes_distribution.png" width="32%">
<img src="{{ site.url }}/assets/images/traffic-sign-classifier-project/test_classes_distribution.png" width="32%">

<br/>
