---
layout: post
title: "20: Template For Arduino Robot Driver"
date: 2016-12-19
catergories: Projects, Electrical, Signals
comments: true
---

The motivation for this post is to provide a very simple template of "nodes" that I always use for projects. After going through various competitions and projects, I realized that all robotic projects can be built off the same model. A core set of function blocks that allow for fast hardware testing and flexibility to expand in software.   


Overall I think the following functions are always necessary:
- Input parsing. May it be from a computer or a radio controller, there robot generally has some sort of way to interact with humans.
- Output. Generally in the form of a PWM signal for motor controllers.
- IO Mapping And Scaling. Depending on the system, you may want your radio signal to map directly to the throttle of your robot, or you may want specific inputs mapped to specific sets of behavior.

## System Hierarchy

Though Arduinos are generally considered the "brains" of the robot, in more complex systems they are more suitable for an adapter between the high level complex computer and the low level circuit. In most cases a computer would send a command via USB to an Arduino (or some microcontroller) that further controls an ESC (Electronic Speed Controller) or H-bridge circuit that provides power to the motor.
Overall the Arduino is in charge of the following tasks:
1. Collecting sensor data
2. Sending sensor data to a higher level system for processing
3. Sending control signals to the motor controllers
4. Receiving control signal (Analog and Digital)
5. In some cases error check and correct its movement based on sensor data

This post will focus on the microcontroller's (Arduino) role in the system in respect to the necessary functions stated in the beginning.

## Input Parsing on Microcontroller

The main inputs for the microcontroller can be both analog or digital. Analog in the case of radio/sensor signals, and digital in the case of any form of serial communication.

In the case of analog signals, the Arduino has a very convenient command [`pulseIn`](https://www.arduino.cc/en/Reference/pulseIn) that can help you with RF signals. It's important to note that even though RF signal are analog, due to the nature of how Arduino reads the signal, the input has to be connected to digital pins on the Arduino. My suggestion is to chose pins that do not explicitly support PWM so you won't have to shuffle pins around in the future.

For sensor signals that you are collecting, check first that the sensor you are using is not using a form of serial communication. Some examples would be the [Adafruit Breakout GPS](https://www.adafruit.com/product/746) that uses serial communication with the Arduino. (This can be easily recognized by the TX/RX pins on the sensor board). In the case of an accelerometer like the [ADXL335](https://www.adafruit.com/product/163) sold by Adafruit, the inputs to your system would need to be connected to the analog pins on your Arduino. After the wiring is done, all you have to do is call the [`analogRead`](https://www.arduino.cc/en/Reference/AnalogRead) function under Arduino's standard library.

For digital signals, [`Serial.read`](https://www.arduino.cc/en/Serial/read) is the function you would want to use to collect data. Here are some things to be careful about:
1. Every time you call the `Serial.read` command or `Serial.available` command, a byte in the input queue is popped. Therefore, be very careful when reading so you don't lose data.
2. If you are getting random values that you are not expecting check that `Serial.begin` is initiated with the correct baud rate.
3. The `Serial.read` function returns an int, if you would like any other format, you would have to perform the conversion yourself.

## Output on microcontroller

The two main outputs the microcontroller for this particular system would be the output to the motor controllers and the output to the computer via USB.

For output to the motor controllers, there are a few methods, however the simplest method is to use the [`servo.write`](https://www.arduino.cc/en/Reference/ServoWrite) command that converts an int into a pwm signal. The parameter you would want to give `servo.write` would be a value between 0-180 with 90 being neutral. Important things to note:
1. In any case where you are not getting the desired output from your motor, double check that the controller you are using has the same duty cycle as your esc.
2. If you are driving a 3 pin servo, 90 would correspond to the arm standing up right. However if you are driving a motor controller, 90 corresponds to 0 throttle.  

USB Serial has also been made easy by [`Serial.write`](https://www.arduino.cc/en/Serial/write). This would be used in the case where you are using the microcontroller as a data collecting adapter to the computer. It is also a very useful function to check if you are sending the correct messages from you Arduino to your computer.

## IO Mapping and Scaling

In most cases your signals generally don't match up between the input to the output when passing through the Arduino. Examples:
1. A radio signal would be in frequency provided by `pulseIn`, however the virtual output of the system would have to be an int between 0-180 for `servo.write`.
2. A turn knob is a potentiometer that would be giving you analog signals.
For all the cases here some sort of scaling or logic would have to be implemented.
Traditionally we would write some long if statement to parse our data and convert it, however Arduino has the [`map`](https://www.arduino.cc/en/reference/map) function that does everything for you.

## Code Recommendation

The general format of your Arduino code would be recommended to include the following:

1. `void setup()` is one of 2 necessary pieces of code for the Arduino. Most of the function declarations would be done in here.
2. `void loop()` is the other necessary function needed. It can be thought of as the equivalent of `main` in C. The suggestion is to first decide the functions that the Arduino has to carry out and write them as separate functions called in `loop`.     
    i. Main logic can be done here, if there are any cases where special functions have to be preformed or overridden, it is a good choice to put it here because it is the highest level loop you can have in an Arduino system.   
    ii. `Input function`. Since in most cases you would be taking in some sort of command, this is a good function to have nested in the loop. But because you would want to write the mapping and scaling as well, it would be wise to separate it from `loop` to avoid convoluting your code.   
    iii. `Ouput function`. Best also nested within loop since this will be done constantly as long as your robot is running.   
