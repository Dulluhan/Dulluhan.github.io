---
layout: post
title: "Template For Arduino Robot Driver: 80"
date: 2017-4-17
catergories: Projects, Electrical, Signals, Arduino
comments: true
---
Refer to [previous post](http://c4coffee.ca/2016/12/19/A-Simple-Arduino-Robot.html) for a general overview.

For the robots our team designs, we need to not only have support for radio controllers, but also computer controllers (ROS). In most cases we have traditionally always used a Turnigy 9x controller, chosing the spring loaded joystick as our movement controller, and the complementary joystick as the mode selector.

More specifically we will have the following parameters to abide to when writing the controller:
1. Forward, Backward and Turning with Neutral Deadzone on Joystick.
2. Mode selector needs to accommodate: (Wireless e-stop, Autonomous Mode, Manual Mode)
3. Read radio signals from the Turnigy
4. Read twist messages from USB Serial

Next the sections will be divided up in respect to the design parameters we have chosen.

## Setup and Main Loop
`void setup()` and `void loop` are the two fundamental necessary components that are needed in any piece of Arduino Code. Where `setup()` is used for declaring objects and opening serial ports, necessary function calls that only need to be done one time, and `loop()` is the main loop that will continue to run after the board gets power. Together they are the `main` equivalent of normal C/C++ code.
{% highlight c linenos %}
void setup() {
  Serial.begin(BAUD_RATE);

  pinMode(2, INPUT);
  pinMode(3, INPUT);
  pinMode(4, INPUT);
  pinMode(5, INPUT);

  LeftM.attach(LEFT_MOTOR_PIN);
  RightM.attach(RIGHT_MOTOR_PIN);

  set_offset();
}
{% endhighlight %}

In `setup()` the pinmodes are specified. In general there is no need for pullup or pulldown resistors for Arduino circuits as Arduino has 1k built in resistors. However, the modes of the digital pins have to be set here in advance to be ready for input.
In this particular case, we have done the following:
1. Set digital pins (d2, d3, d4, d5) to Input mode, necessary for measuring the RF signal from the RC antenna.
2. "Attached" our motor outputs, which are just abstracted declarations of the motor objects. These objects hold the functions necessary to control the individual output motor controllers. Important to note here that `LEFT_MOTOR_PIN` and `LEFT_MOTOR_PIN` are to be declared different to the input pins (d2, d3, d4, d5) and specifically chosen from one of the pins that support PWM output. Pins that support PWM are noted by a "~" on the board. It is also general practice that symmetrical systems are wired up in pairs. ie. For our system with 2 motors we will chose either [d3,d5], [d6, d9] or [d10, d11] as these 3 pairs are all of different clock zones.
3. Function `set_offset()` is called. This is to initialize the sensor values to neutral, in other words we are zeroing the sensors. In our particular case, we are setting the neutral frequency we want as the center of the deadzone. The iplementation will be discussed in detail underneath

{% highlight C linenos %}
void loop() {
  rc_read();
  if (Mode == 1) { //Autonomous
    serial_read();
    convert();
    drive(linear_x, angular_z);
  }
  else if (Mode == 0) {//RC Mode
    linear_x = range1;
    angular_z = range2;

    convert();
    drive(linear_x, angular_z);
  }
  else {
    linear_x = UNMAPPED_STOP_SPEED;
    angular_z = UNMAPPED_STOP_SPEED;

    convert();
    drive(linear_x, angular_z);
  }
}
{% endhighlight %}

In the main loop, we are constantly checking the mode of the system set by the remote control through `rc_read()`. There are 3 modes as discussed earlier:
#### Mode 1: Autonomous Mode
1. Read the command sent to the Arduino via USB.
2. Convert the commands to the appropriate range. This includes any speed clamping necessary for safety and competition rules.
3. Send scaled output to motor controlling function.

#### Mode 0: RC Mode
1. Read the input from radio for driving.
2. Convert the commands to the appropriate range.
3. Send scaled output to motor controlling function.

#### Mode Default: E-stop
1. Set everything to 0.
2. Scale the commands to appropriate range. In this case it would be 90 for `servo.write()`.
3. Send scaled output to motor controlling function.


## Read Radio Signal
{% highlight C linenos %}
void set_offset() {
  delay(100);
  int linear_x_mid = pulseIn(2,HIGH); //RX standard - radio signal midpoint
  int angular_z_mid = pulseIn(3,HIGH); //RY standard - radio signal midpoint

  linearXHigh = linear_x_mid + JOYSTICK_MARGIN;
  linearXLow = linear_x_mid - JOYSTICK_MARGIN;

  angularZHigh = angular_z_mid + JOYSTICK_MARGIN;
  angularZLow = angular_z_mid - JOYSTICK_MARGIN;
}
{% endhighlight %}
`void set_offset()` is necessary as the system's (RC receiver's) frequency is not necessarily constant. It can easily change due to interference, change in receiver or even different microcontroller. Therefore, every time we startup the system, we will measure the frequency reading when the remote control is in neutral position. This will allow us to scale the deadband accordingly.

Generally the only thing that changes is the center frequency of specific channels. Therefore there is technically no "scaling" done, only translations of the range.
{% highlight c linenos %}
void rc_read() {
  range1 = pulseIn(2, HIGH); // Forward/Backward
  range2 = pulseIn(3, HIGH); // Turn
  range3 = pulseIn(4, HIGH); // Mode

  if (range1 < linearXHigh && range1 > linearXLow)
    range1 = UNMAPPED_STOP_SPEED;
  else
    range1 = map (range1, 949, 1700, 0, 255);

  if (range2 < angularZHigh && range2 > angularZLow)
    range2 = UNMAPPED_STOP_SPEED;
  else
    range2 = map (range2, 949, 1700, 0, 255);

  if (range3 < 1200)
    Mode = -1; // STOP mode
  else if (1200 <= range3 && range3 < 1600)
    Mode = 0; // RC mode
  else if (1600 <= range3 && range3 < 2000)
    Mode = 1; //auto mode
  else
    Mode = -2; // STOP mode

  if (abs(range1 - 90) < TRIM)
    range1 = UNMAPPED_STOP_SPEED;
  if (abs(range2 - 90) < TRIM)
    range2 = UNMAPPED_STOP_SPEED;
}
{% endhighlight %}
The first thing done in `rc_read()` is to measure the frequency received from the individual channels. The Arduino does this by comparing it's own clock pulses to the input pulses, a process abstracted in `pulseIN()`.

Next we map the frequencies read for the 3 individual channels (range1, range2, range3) to our desired range. For range1 and range2 it is in the range [0,255] because that is the range specified for twist messages, to make the conversion easier, we have decided that the `convert()` function should only have to deal with one specified range for simplicity.

For range3, the desired range would be to split the frequency spectrum into 3 sets. However the code above is probably not the optimal implementation. The optimal implmentation would look more like:
{% highlight C linenos %}
if (abs(range3-mode_mid) < deadband)
  Mode = 0;
else if (mode_min < range3 < deadband+mode_mid)
  Mode = 1;
else
  Mode = -1
{% endhighlight %}
Where Mode 0 is autonomous mode, Mode 1 is RC mode, and Mode -1 is E-stop mode. This will ensure that anything outside of the range [mode_min, mode_mid+deadband] is E-stop so the robot does not move. This should be considered a necessary safety feature so that any spikes in the transceiver frequency will not make the robot move. (This commonly happens when you turn the remote on and off).
## Read Twist Messages
{% highlight C linenos %}
void serial_read(){
  if (Serial.available() > BUFFER_SIZE) {
      if (Serial.read() == BUFFER_HEAD) {
          linear_x = Serial.read();
          linear_y = Serial.read();
          linear_z = Serial.read();
          angular_x = Serial.read();
          angular_y = Serial.read();
          angular_z = Serial.read();
      } else {
          linear_x = angular_z = UNMAPPED_STOP_SPEED;
      }

  } else {
      linear_x = angular_z = UNMAPPED_STOP_SPEED;
    }
}
{% endhighlight %}
Reading of the twist message is straightforward. `Serial.read()` reads exactly one byte from the serial queue. Like most read functions it will pop the byte of the queue read, so be careful when handling. In the specific case of our function `serial_read()` the following steps are performed:
1. `Serial.available()` returns the number of available bytes. The if statement on line 2 will check and confirm that we have received a full set of twist messages before proceeding.
2. The first byte on the serial input queue is checked to see if it holds to the symbol chosen as the message head
2. The individual bytes of (linear x,y,z and angular x,y,z) are received via `Serial.read()`
3. In any case where the initial byte is not the message head, or if the full twist message has not arrived, all values will just be set to neutral.

A twist message is specified in the form: `'B',int,int,int,int,int,int`, where `'B'` is just a char to indicate the beginning of the message, and following 'B' would be a chain of 6 one byte ints that correspond to the linear x,y,z and angular x,y,z values. So an example received message would look like: `B12818212812812890`. This would indicate that the computer is asking the robot to move in the linear_y direction (forward for robot) and turn to the left. "128" would be considered neutral as it is the center of the range [0,255], a 1 byte int.

## Drive Functions
{% highlight c linenos %}
void convert() {

  if (linear_x > 255)
    linear_x = 255;
  else if (linear_x < 0)
    linear_x = 0;

  if (angular_z > 255)
    angular_z = 255;
  else if (angular_z < 0)
    angular_z = 0;

  linear_x = map(linear_x, 0, 255, LINEAR_MIN, LINEAR_MAX);
  angular_z = map(angular_z, 0, 255, ANGULAR_MIN, ANGULAR_MAX);
}
{% endhighlight %}
`void convert()` takes the 1 byte ints and converts them to the domain required for function `servo.write()` that may be used in`servo_write()`.
{% highlight c linenos %}
void drive(int linear_speed, int angular_speed){
  int left_throttle = linear_speed + (angular_speed - ANGULAR_STOP);
  int right_throttle = linear_speed - (angular_speed - ANGULAR_STOP);

  servo_write(LeftM, left_throttle);
  servo_write(RightM, right_throttle);
}
{% endhighlight %}
The `drive()` function combines the angular and linear commands into the respective left wheel throttle and right wheel throttle needed to carry out the action.
{% highlight c linenos %}
void servo_write(Servo motor, int throttle) {
  throttle = map(throttle, 70, 110, 1000, 2000);
  motor.writeMicroseconds(throttle);
}
{% endhighlight %}
The `servo_write()` can be implemented in two ways. One is to directly write the duty cycle as demonstrated in the code above. The other is to utilize the existing `servo.write()` function built into the standard Arduino libraries. The direct method is chosen in this case because of our ESC's duty cycle not matching the output of `servo.write()`

## Setup
The library files needed are the following:
{% highlight C linenos %}
#include <SoftwareSerial.h>
#include <stdlib.h>
#include <Servo.h>
{% endhighlight %}

Constants to define:
{% highlight C linenos %}
#define TRIM 8

#define BAUD_RATE 9600

#define BUFFER_SIZE 6

#define UNMAPPED_STOP_SPEED 128
#define OFFSET 1
#define JOYSTICK_MARGIN 50
#define RIGHT_MOTOR_PIN 10
#define LEFT_MOTOR_PIN 11
#define BUFFER_HEAD B

#define LINEAR_MAX 100
#define LINEAR_MIN 80
#define LINEAR_STOP 90

#define ANGULAR_MAX 100
#define ANGULAR_MIN 80
#define ANGULAR_STOP 90
{% endhighlight %}

Variable Declarations:
{% highlight c linenos %}
int linear_x = 0;
int linear_y = 0;
int linear_z = 0;
int angular_x = 0;
int angular_y = 0;
int angular_z = 0;

Servo LeftM;
Servo RightM;

int range1 = 0, range2 = 0, range3 = 0, B2 = 0, B4 = 0, Mode = 0;

int linearXHigh = 0, linearXLow = 0, angularZHigh = 0, angularZLow = 0;
{% endhighlight %}

---
Code can be found at our [team Github](https://github.com/UBC-Snowbots/IGVC-2017/tree/master/src/firmware/firmware_control)
Authors of code: Vincent, Nick, James
