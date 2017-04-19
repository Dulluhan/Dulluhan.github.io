---
layout: post
title: "DIY Crystal AM Radio Design - Part1: Background"
date: 2016-10-30
tags: Projects Electrical
comments: true
---

<p>As part of a project course over the summer, we were tasked to build a self-powered AM radio with easy assembly. The motivations were as follows:
<ul>
  <li>Easy assembly allows good education material for younger children</li>
  <li>Self-powering allows for use during emergency situations</li>
  <li>There is good AM radio coverage in the Vancouver area</li>
  <li>FM radios are slightly more complicated than AM radio</li>
</ul>

Below are sections extracted from our team's final report.</p>

## Construction Background

Shown below is a block diagram of what we recognized as the major components required for the construction of an AM radio.

![]({{site.urlt}}/img/2016-10-30.png)

The <b>RF(Radio Frequency) receiver</b> is an electrical component that captures electromagnetic waves in the radio frequency range and converts it to electrical power.   
Our <b>RF selection mechanism</b> is essentially a frequency filter that will allow us to choose the frequency at which we are listening to. It is important to note that in most cases, the RF receiver does not exclusively receive one single channel, therefore a filter is required to filter out other channels we do not want.   
The next important step is to amplify the RF signal received. But from research it was realized quickly that RF amplification was not an easy task, and that for the applications of our project it was unnecessary.    
Since there was no RF Amplification, <b>AF(Audio Frequency) Amplification</b> was necessary after <b>Demodulation</b> where the carrier signal was separated from our modulated signal.   

## Signal Processing Background

The most important concept to learn for this project was the theory behind modulation. In the simplest terms, AM modulation simply meant that we have a very fast oscillating <b>carrier signal</b> that was representing itself as our <b>information signal</b>, which are the audio signals from the radio stations in our case.   

<img style="display: flex; justify-content: center;" src ="https://upload.wikimedia.org/wikipedia/commons/a/a4/Amfm3-en-de.gif">   

To understand <b>demodulation</b> it would be important to understand how to interpret the signals. Sine signals can be represented by two peaks at +/- of their frequencies. Like below:

![mathworks](http://blogs.mathworks.com/images/steve/2009/F_cos_t.png)

With the combination of our modulated signal and our carrier signal, we will get something that looks more like:

![wikipedia](https://upload.wikimedia.org/wikipedia/commons/thumb/a/ae/AM_spectrum.svg/600px-AM_spectrum.svg.png)

Where the signal above is the signal we are modulating, and the signals below is our same signal after modulation. It can be observed that the original signal centered is now separated into two signals at higher frequencies. This is because, our original signal is now being represented by a signal that oscillates at higher frequencies.

---
Image sources:
<ul>
  <li><a href="https://en.wikipedia.org/wiki/File:Amfm3-en-de.gif">Wikepedia: Amplitude Modulation GIF</a></li>
  <li><a href="http://blogs.mathworks.com/steve/2010/05/27/negative-frequencies/">Mathworks Blogs: Steve Eddins on Negative Frequencies</a></li>
  <li><a href="https://en.wikipedia.org/wiki/File:AM_spectrum.svg">Wikipedia: Amplitude Modulation AM Spectrum</a></li>
</ul>

---
Part 2 can be found at: [http://c4coffee.ca/2016/11/12/diy-am-radio-part2.html](http://c4coffee.ca/2016/11/12/diy-am-radio-part2.html)
