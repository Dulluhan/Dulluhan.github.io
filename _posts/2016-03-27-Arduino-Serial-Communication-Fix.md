---
layout: post
title: "Arduino Serial Communication Problem and Fix" 
date: 2016-03-27 
catergories: Arduino
comments: true
---

Since the primary work I do is interfacing ROS with microcontrollers for sensor/actuator controls, I would like to share a problem that you may often encounter when doing any time of data transmission.

Of course there may be more elegant ways of solving this issues if these are even issues. But from what I observed before and after the fixes, my hypothesis for the problems seemed to be solved. 

# Buffer Flushing for arduinos
Problem: When ROS loops are running at a frequency much higher than the arduino can process incoming commands, you may get problems with data in your buffer either holding too much, or not being processed correctly. 

Situation: Our method of actuator resolves around sending custom [twist messages](http://docs.ros.org/api/geometry_msgs/html/msg/Twist.html) that have the format of: (Lx, Ly, Az). Where Lx/Ly is linear x,y and Az is angular z. What they give us are the motion commands of moving forwards, sideways and turning.  

When the robot is hooked up to our laptop via USB, we are constantly bombarding the arduino serial port with multiple twist messages a second. I have observed that under certain circumstances, the arduino will either process messages with slight delay to the computed twist messages, or it will competely "hang" and freeze the serial port. 

Of course the first solution I thought of was to flush the buffer via `Serial.flush();` for the arduino to always process the newest data. However, what I did not know was that the arduino's flush function under the HardwareSerial.h library was flushing the output buffer not the input buffer, therefore I had to dig around on the internet to see what [others have done to fix this problem.](https://forum.sparkfun.com/viewtopic.php?f=32&t=32715)

Solution: Adding a function to the HarwareSerial.cpp file in the Arduino library files. 

For the .h, add a new function declaration `virtual void flushRX();`

For the .cpp, add the rest of the function: 
{% highlight c++ %}
void HardwareSerial::flushRX(){
  _rx_buffer->head = _rx_buffer->tail; 
}
{% endhighlight %}
