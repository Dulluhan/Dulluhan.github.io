---
title: 80/20 Knowledge for Sensor Fusion - Statistics Part 1
date: 2018-04-18
tags:
  - Robotics
  - Sensors
published: true
layout: post
comments: true
---
This post is supposed to detail what I think covers 20% of the concepts used 80% of the time when dealing with sensors integration. 
To start, there are most certainly better resources out there that explain these concepts better. I will link those on the bottom of the post. Next, this most likely will not cover everything you need to know, just the basics of statistics. 
--- 
## Accuracy and Precision

Before we start, I would like to make sure we understand the difference between *accuracy* and *precision*. Being accurate refers to how close our measured value to is to "the truth". Precision on the other is all about the consistency of data we are receiving. 

This distinction is important because with Sensor Fusion, we are extracting higher accuracy by combining multiple sensors measuring the same thing. In the process of doing so, we have to account for the precision of our sensors. We cannot simply assume the accuracy of a sensor even if it was precise, hence the need for calibration on all the sensors we use. For sensors, precision is static relative to the operating point you chose. In other words precision is not something we can improve
without making a new sensor. On the other hand, accuracy is something that the end stream engineers like us can improve on. Improving accuracy would require more sofisticated techniques detailed below. 

--- 
## Statistics and Probability 

Statistics is the branch of mathematics that study data, probability details the odds of events happening. They come together because most things in life are not exact, and comes with uncertainty, that includes data we work with. Therefore, while we are working with processing the data that we collect over time in our robot, it is important to consider that odds of things happening or behaving a certain way. In other words, it is important for us to be able to recognize, filter and correct
data that is not following the trends (probability) that we are expecting.  

![]({{site.urlt}}/img/gaussian.png)

Quick example to illustrate this. If we have a sensor that tells us the position of a robot, continuously, updating us with (x,y) coordinates, and we want to use this data to navigate, we would need to make sure that the data we receive is [correct](accuracy-and-precision). How do we know if we are receiving correct data? We can do it base off of the probability distribution of the data. In other words, we can figure out if we are receiving correct data by simply guessing if the data point
is "realistic". If we get coordinate updates telling us that the robot is in a position that clearly is in the position of a wall, then we know that the data is most likely wrong. 

---
## The Normal Distribution 

The Normal Distribution is also known as an Gaussian distribution. 
This is what we guess most sensor uncertainty spreads look like. 

![]({{site.urlt}}/img/Gaussian_intro.png)

Math aside, translating the statistical language to english. The "uncertainty" labels you see you specficiation sheets will give you a specific value on the maximum and minimum deviations you may get for a particular measurement. This could be taken a few ways. One is to read this as the maximum and minimum ends of the bell curve. However, the problem is that the bell curve converges towards the x-axis, but there is no good place to chose a cutt-off point to place the maximum and minimum
values. Since sensor data, or real life data does not come in a [uniform distribution](https://en.wikipedia.org/wiki/Uniform_distribution_(continuous)), we will have to consider how to map the maximum and minimum values of our sensor data to the bell curve. 

[Now, ISO does have specified ways of defining uncertainty and error in measurement.](https://www.iso.org/sites/JCGM/GUM/JCGM100/C045315e-html/C045315e_FILES/MAIN_C045315e/01_e.html) But unless you working in safety engineering, its not worth your time. 

In general, we will chose our variance to be the square of our uncertainty value. The reason of which is so our standard deviation (Square root of variance) will have the same unit as our measured value. eg. If we measure Length=1.2+/-0.2m, then our variance will be 0.04m^2, while our standard deviation will be +/-0.2m.

---
### Bayes' Theorem and Markov 

Now, [Bayes' Theorem](https://en.wikipedia.org/wiki/Bayes%27_theorem) is why stastics work. It's the bread and butter to making predictions. Basically Bayes' Theorem is about the chances of a particular event happening given the probability of a causal event. For us, it means that we can guess if a sensor should be getting a particular reading based on the previous reading, or better yet, if we are in a particular location given our sensor measurements and previous input state. 

![](https://upload.wikimedia.org/wikipedia/commons/thumb/1/18/Bayes%27_Theorem_MMB_01.jpg/220px-Bayes%27_Theorem_MMB_01.jpg) 

What goes hand in hand with Bayes' Theorem is the concept of Markovian Processes. A Markov chain describes the transition of different "states" of a system, assuming causality between two states. For example, a Markov chain can describe my actions in the day by prediction of states. Say I was in state 1: "Awake and out of bed", then there may be a 90% probability that I transition to state 2: "Go to Starbucks to buy a coffee", or a 10% change that I transition to state 3: "Go back to
bed". 
The key to a Markovian processes is that, it doesn't matter what I was doing before I woke up and got out of bed, on any given day, there will always be a 90% chance that I go to Starbucks. This is the concept of memorylessnes. An example commonly used is Brownian Motion, particle movements are random, and only depend on their last interaction with another particle to determine the next step of where it's going. 
Similarly, the state estimator of a robot does not know the path planned or the actual path taken. So to our state estimator (Particle filter, Kalman Filter, Bayesian Estimator...etc) it can make pretty good guesses on what the robot is going to do next based on the current state. 

![]({{site.urlt}}/img/histogram.png)

This will allow us to create things like histogram filters with fixed transition probabilities shown above. 


---
### Example 

![]({{site.urlt}}/img/posterior_est.png)


--- 

## Notes 
Skip ahead to read about Kalman Filters [here](http://www.bzarg.com/p/how-a-kalman-filter-works-in-pictures/)

---
