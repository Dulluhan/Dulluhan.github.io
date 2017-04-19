---
layout: post
title: "Troubleshooting Arduino and RC Receiver"
date: 2017-4-19
tags: Projects Electrical Signals Arduino
comments: true
---
Say you do everything in the [previous post](http://c4coffee.ca/2017/04/17/Arduino-Driver.html) but things aren't working. Or if you wanted a good challenge and unplugged the antenna from the circuit. Possibly even the chance that you just hate Turnigy and got a different remote. Below are some tips and notes for trouble shooting the system:

- I don't know which pin is receiving which signal: refer to ["Testing the Input Signal"](#Testing)
- I am not sure if I am even receiving a signal: refer to ["Wiring"](#Wiring)


## <a name = "Testing"></a>Testing the Input Signal
Below is a nice piece of code that can be used to check the input signals. The steps for running this is:
1. Plug in all the wires from receiver to remote.
2. Plug in Arduino, flash the code and open serial monitor.
3. Turn on Radio and check which channels are changing.

If in any case the channel set to the pin is incorrect, swap the wires, or change the code.

{% highlight c linenos %}
#define UNMAPPED_STOP_SPEED 128
#define JOYSTICK_MARGIN = 50;

int range1 = 0, range2 = 0, range3 = 0, B2 = 0, B4 = 0, Mode = 0;
int linearXHigh = 0, linearXLow = 0, angularZHigh = 0, angularZLow = 0;

void setup() {
  Serial.begin(9600);
  set_offset();
}
void set_offset() {
  delay(100);
  int linear_x_mid = pulseIn(2,HIGH); //RX standard - radio signal midpoint
  int angular_z_mid = pulseIn(3,HIGH); //RY standard - radio signal midpoint

  linearXHigh = linear_x_mid + JOYSTICK_MARGIN;
  linearXLow = linear_x_mid - JOYSTICK_MARGIN;

  angularZHigh = angular_z_mid + JOYSTICK_MARGIN;
  angularZLow = angular_z_mid - JOYSTICK_MARGIN;
}
void loop() {
  rc_test();
}
{% endhighlight %}
The code above is quite straightforward, very similar to the setup seen in the [previous post](http://c4coffee.ca/2017/04/17/Arduino-Driver.html) that implements actual movement of the robot.

## Function for Testing RC
Below in the funtion `rc_test()` called in `loop()`, we simply take in the RC signals read from the pins and output them through serial so you can read it in the terminal. Arduino has a built in serial monitor that can be opened via toolbar `tools->Serial Monitor` or by pressing `Ctrl + Shift + M`.
{% highlight c linenos %}
void rc_test(){
  range1 = (pulseIn(2,HIGH));
  range2 = (pulseIn(3,HIGH));
  range3 = (pulseIn(4,HIGH));
    if (range1 < linearXHigh && range1 > linearXLow)
    range1 = UNMAPPED_STOP_SPEED;
  else
    range1 = map (range1, 949, 1700, 0, 255);
  Serial.print("Throttle: ");Serial.print(range1);
  if (range2 < angularZHigh && range2 > angularZLow)
    range2 = UNMAPPED_STOP_SPEED;
  else
    range2 = map (range2, 949, 1700, 0, 255);
    Serial.print(" Turn: "); Serial.print(range2);

  if (range3 < 1200)
    Mode = -1; // STOP mode
  else if (1200 <= range3 && range3 < 1600)
    Mode = 0; // RC mode
  else if (1600 <= range3 && range3 < 2000)
    Mode = 1; //auto mode
  else
    Mode = -2; // STOP mode
  Serial.print(" Mode: ");Serial.println(Mode);
}
{% endhighlight %}

## <a name = "Wiring"></a>Wiring

If you are unsure about the wiring follow these rules to trouble shoot:
1. Always connect 5V from Arduino to + pin on receiver, and GND pin from Arduino to - pin on receiver. You will only need one pair of these power wires going to the receiver.
... - This can be check by seeing if the remote lights up when you turn the radio on. If they are a proper remote-receiver pair, then there should be a faint light below the front sticker on the receiver. If it does not light up, then you may have the wrong remote-receiver pair or a broken receiver.
2. Check that the signal pins are plugged in to digital pins you have assigned (Not the analog pins, despite radio signals being analog) to in the Arduino code.
... - This can be check by verifying that you are getting a signal via the code above. If you are getting no changes in the serial monitor, there might be a problem with the wiring.


If you do not have a spare Arduino by you, you can always plug the PWM signal of an ESC into the receiver to see if it is capable of driving the motor directly.
---
A copy of the code can be found [here](https://gist.github.com/Dulluhan/263f37e77abf15455d012e6c37df23db)
