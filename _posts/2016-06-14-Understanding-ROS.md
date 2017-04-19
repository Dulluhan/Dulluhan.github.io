---
layout: post
title: "Understanding ROS"
date: 2016-06-14
tags: ROS
comments: true
---
<p> Recently I've been reached out via email, A2A about the function of ROS. For people who are new to ROS, it is hard to grasp what ROS is and why we need it. Below is a copy of the question, link to the question, and my answer. </p>

<p align="center">~~~</p>  

# [Question: What is the role of ROS in a complete robot?](http://answers.ros.org/question/237411/what-is-the-role-of-ros-in-a-complete-robot/#237420)

Hi,

After raising this question, I've realized that, unlike what I've known as an iOS developer that iOS is almost the only thing we developers should concern, ROS itself is not an operating system but a framework, a set of libraries. And besides that, what a robot exposes to developers are far more than an iPhone does. So these are all new concepts for me to absorb and it may take a while ;)

My question is, what's the role of ROS in a robot?

Can someone please give us a detailed explanation of what a robot is generally comprised of, with examples? Say in Roomba, I know it has its own operating system; what part of Roomba plays a similar role as ROS?

Thanks,

snakeninny

<p align="center">~~~</p>

Hello snakeninny,

At this point in your research I hope you now understand the concept of nodes and packages in ROS, or simply the existence of them. Due to the complexity of a lot of the decisions made or computations being performed by the robot, it is no longer feasible for all of the software to be written in one package. Therefore a lot of functionalities are grouped into these nodes and executed together.

From your background, you can understand these nodes as objects that does different things. Eg. A node that processes computer vision; an eye object. A node that processes speech; an mouth object. A node that processes actuator controls, a cerebellum object. Objects in a OOD sense.

What ROS as an "operating system" does is to facilitate a framework where these nodes can all communicate with one another to perform complex tasks that requires simultaneous stimuli from different sensors and perform calculations.

The Roomba is a very simple robot compared to other systems you may see being created with ROS. A Roomba replica can easily be created with an Arduino. It's like comparing a goldfish to a human brain. We can no doubt sleep, eat and move as a goldfish can; just like how a ROS system can do what a Roomba does. But in cases where you want to be able to tell the difference between what you are eating, or being able to dance, a goldfish cannot do that.

In most cases, I like to think of micro controllers like the arduino as controllers for individual components, taking orders from individual nodes. Eg. A movement node says it wants to move forward, and the arduino controls the ESC on the robot to move the motor. The arduino knows how to turn, how to activate motors, but it will not be smart enough to know how to move to properly tango.

A usual chain of command in systems I work with looks something like:

Sensors detect object
AI node uses other sensors to decide what type of object it is
AI decides that it needs to move left-forward-right to avoid the object
Movement node receives left-forward-right command from AI
Movement node sends left, forward, right as seperate commands to the Arduino that controlls the motors, checking between commands that the robot is moving correctly.
All this is based off of my own understanding and experience with ROS, and ROS can certainly be more complex or simple depending on the application. But hopefully this is a starting point for you.

Cheers, Dulluhan

<p align="center">~~~</p>
