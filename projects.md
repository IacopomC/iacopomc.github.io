---
layout: home2
permalink: /projects/
title: projects
tags: [projects]
comments: false
---

## Graduate Researcher 

<p style="text-align: center;">
<b>Latent Question Interpretation Through Parameter Adaptation</b>
</p>

<br /> 

<img src="/images/apip.jpg" alt="hi" class="inline"/>


<p style="text-align: center;">
<a href="https://github.com/parshakova/APIP">[Code]</a>
</p>

Most artificial neural network models for question-answering rely on complex attention mechanisms.
These techniques demonstrate high performance on existing datasets, however, they are limited in their ability to capture natural language variability, and to generate diverse relevant answers. 
To address this limitation, we propose a model that learns multiple interpretations of a given question. 
This diversity is ensured by our "interpretation policy" module which automatically adapts the parameters of a question answering model with respect to a discrete latent variable. This variable follows the distribution of interpretations learned by the interpretation policy through a semi-supervised variational inference framework.

<br />

<br />

<p style="text-align: center;">
<b>Neural Opinion Generator</b>
</p>

<br /> 

<img src="/images/opnet.jpg" alt="hi" class="inline"/>

<p style="text-align: center;">
<a href="https://github.com/parshakova/neural_opinion_generator">[Code]</a>
</p>

“Opinion Generator” is a model, which aims to capture a global ‘pathway’ of an opinion as a response to a given statement. It consists of CNN encoders, that operate on character level, and whose outputs are given to recurrent block to combine sentences over time so that the produced context representations are used to condition the CNN decoder for character level generation (or LSTM for word level). Self-critical policy gradient is used to maximize the reward expectation of our model. To train our model we create a dataset using the “Change My View” Reddit forum that consists of opposing views. It is an appropriate dataset for training the Opinion Generator since it contains discussions on various topics with the main goal to understand opposing viewpoints.

<br />

<br />

<p style="text-align: center;">
<b>Playing Angry Birds: Deep Deterministic Policy Gradient with Attention-based Long Short-Term Memory</b>
</p>

<br /> 

<img src="/images/angryb_model.jpg" alt="hi" class="inline"/>

<p style="text-align: center;">
<a href="https://github.com/parshakova/AngryBirds_RL">[Code]</a>
</p>

"Angry Birds" is a game with a continuous action and state space. The policy gradient methods proved their effectiveness in a number of various domains. However, for a stochastic policy, the variance of a gradient estimate goes to infinity as policy becomes more and more deterministic.
Therefore, we develop a sequential decision-making agent for solving ‘Angry Birds’ using Deep Deterministic Policy Gradient (DDPG) with Attention-based Long Short-Term Memory (LSTM) for state encoding. In other words, we explore a model with a deterministic policy using an actor-critic algorithm for learning off policy with a stochastic behavior policy.

<br />

## Undergraduate Researcher 

<p style="text-align: center;">
<b>Ratchair: Furniture that Learns to Move Itself</b>
</p> 

<iframe width="560" height="315" src="https://www.youtube.com/embed/bbBSVTTapp0" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/_Cn_ajBpr3A" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

<br />

Displacements of large objects induced by vibration are
a common occurrence, but the motion is generally
unpredictable. Think for instance of an unbalanced
front-loading washing machine. For controlled
movement, wheels or legs are usually preferred.
<br><br>
In this work, we explore a strategy for moving
everyday objects by introducing or harvesting random
external vibration rather than using a mechanical
system with wheels. We designed vibration modules
that can be easily attached to furniture and objects.
After learning how several random bursts of vibration
affect its pose, an optimization algorithm discovers the
optimal sequence of vibration patterns required to
(slowly but surely) move the object to a specified
position.

<br />

<br />

<p style="text-align: center;">
<b>UMorph: Self-Change Tracker to Reflect Yourself to the Future and Past</b>
</p> 


<iframe width="560" height="315" src="https://www.youtube.com/embed/fpCcpROzxiE" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>
<br />
Taking photos has become not only a daily but also an
automatic activity. But how do we use all these
pictures? Storage and retrieval are certainly not as well
developed as capturing. Storage systems organize
photos in the temporal dimension, for example, the
Facebook service “Year in Review” or in visual diaries,
but typically do not support a systematic comparison.
<br><br>
That was the inspiration for this demonstration,
employing automatically taken photographs and
arranging them so that one could easily go back in time
to reflect oneself to the future and past. This is useful
for tracking well-being and body changes using time
physicalization with a natural interface, as well as an
unobtrusive way for systematic capturing of facial
images over time.

<br />

<br />

<p style="text-align: center;">
<b>Luna, Pocket Universe</b>
</p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/ySlNFAfA5Kg" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

<br />

Each pixel of 8x8 dot matrix lives by rules of Conway’s game of life, so-called cellular automaton.
In Conway’s model, different patterns are like species, they are developed in response to evolution.
Therefore Luna simulates the biological system. So it is real universes with organisms that have own
evolution. The device is called Luna, because this universe is going to be with a wearer just as Moon with
Earth, reminding a user that s/he is not alone having alive satellite nearby.
<br><br>
Luna is a world of living organisms. To provoke Big Bang user needs to shake sphere and a new
world will be born in a random manner. Big Bang is accompanied by explosion animation. The device has a tilt
ball switch for recognizing shakes.
<br><br>
A creator can also exhale her spirit into Luna to make the self-sustaining universe. In this case, blowing
is recognized my a microphone amplifier and one pattern among previously prepared never-dying
patterns will be displayed. Breathing is accompanied by an animation of expanding circles.
Luna is a form of everyday jewelry with an unlimited amount of species and lives.

<br />

<br />

<p style="text-align: center;">
<b>Roll the Eyeball</b>
</p>


<iframe width="382" height="315" src="https://www.youtube.com/embed/2GEP5hpyq-4" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

<br />

The device is called  “Eyeball”,  it is an extension of your natural eye,  since literally, it replaces your eyes with itself.
<br><br>
The idea is in letting yourself explore secret places which cannot be  entered with an entire body.  An additional feature of the Eyeball is that it moves by rolling, such that a world it streams is rolling as well, which gives rich disorienting and mismatching effect. Hence, in order to understand streamed data user has to compensate a mismatching information by thinking and by other senses.  This is a nice exercise for your brains.
<br><br>
In the game “Perception”, a user has to observe a hidden place and find a secret message.  In our game, this message is “Hate is love” because of a word  “hate” that is written on the wall, and which turns into “love” in reflection. This is exactly what happens when you look at things under new angles or their reflections.  The purpose of the game is to make a person realize that if s/he changes the way to look at things,  the things s/he looks at will change.
<br><br>
“Roll  the  Eyeball”  is composed of several  parts:  Eyeball,  device  to  control  a locomotion  of  ball  placed in  one  of  user’s  hands  (smartphone  with  Android  app  running  on  it),  device  which  shows  streamed  video  fixed  in  front  of  one  of the user’s  eyes  like  a  pirate  band  (smartphone  with  Android  app  running  on  it  behind  biconvex  lens),  and  a secret  place  itself (1m x 0.8m x 1.5m  box).
