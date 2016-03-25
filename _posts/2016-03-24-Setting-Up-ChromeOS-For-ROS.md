---
layout: post
title:  "Setting Up ChromeOS for ROS"
date:   2016-03-24
categories: ROS
comments: true
---

This post will be focused on listing the preliminary setup requied to facilitate ROS on Chromebooks. The Chromebook that I will be using through this post will be a c720 (2gb/2995U).

<p align="center">~~~</p>

To start it is important to discuss the different options that are offered to Chromebooks for running Linux. If your are just getting into the Chromebook lineup, you probably have heard about Chrubuntu and Crouton. Here are my 2cents on the differnce:   
  
# [Chrubuntu](https://www.reddit.com/r/chrubuntu/comments/1rsxkd/list_of_fixes_for_xubuntu_1310_on_the_acer_c720)  
<p> Chrubuntu is essentially a hack to bring any Linux distro of your choice in replacement of ChromeOS that comes with your chromebook. I have used this for the longest time in the past and it has caused me more problems than not. Particularily hardware compatibiltiy is the primary concern. From fixing the trackpad to optimizing chrome on a clean linux install, its just not worth the effort in my personal opinion. 
</p>

---

# [Crouton](https://github.com/dnschneid/crouton)  
<p>Crouton stands for ChRomium Os Universal chrooT envirONment. Understanding crouton from the surface is that you are essentially running ChromeOS parallel to a distro of your choice. I resorted to Crouton again after a whole year of Chrubuntu simply because I was sick of dealing with the trackpad and processor problems after os updates with Chrubuntu.     
</p>

<p align="center">~~~</p>

In the end of the day I picked crouton, and what I am left with all the benefits of optimized software from Chromebook and the power of a fully functional linux distro. Besides, having a chromeOS operating a robot is hilarious. 

Now to discuss somethings that will make your ROS setup and usage smooth: 


# [xiwi](https://github.com/dnschneid/crouton/wiki/crouton-in-a-Chromium-OS-window-(xiwi))

Xiwi is just X11 in a window. Rather than having to switch between workspaces using the ctrl+shift+alt+F1 "shortcut", you can just have your chroot (crouton instance) running in a window. The whole concept is just -x forwarding your workplace to the chromOS bash shell. 

To install xiwi you can include the installation of it at the initial setup of your chroot.  
`$ sudo sh ~/Downloads/crouton -t xiwi,xfce`  
In the case where you decide to use xubuntu as your chroot distro.

Alternatively you can upgrade your current chroot. For example, if I was using ubuntu precise, I can update it via: 
`$ sudo sh ~/Downloads/crouton -n precise -u -t xiwi` 

After installation, if you would like to run instances of your chroot you can just -x forward it to xiwi: 
`$ sudo distro -X xiwi`  eg. $ sudo startxfce4 -X xiwi

If you just want to run the CLI, you can run: 
`$ sudo enter-chroot` or `$ sudo enter-chroot -n chroot_name`

You can even run specific programs like so: 
`$ sudo enter-chroot` followed by `$ xiwi -T arduino`(tab) or `$ xiwi -F arduino`(window) after you enter your chroot. Further, by adding `&` to indicate that you want to run the program with the terminal prompt still being available. 
Forwarded programs can be killed via `$ kill` or simply closing the window/tab. 

---

# ROS Installation Notes

One of the only crippling problem that took me a bit to fix was that ROS could would not install on xfce that I had setup on my initial install of crouton. After some research I realized that the version of xubuntu I was running was not supported. It is important to note that I am setting up ROS-indigo for compatibility with my teams' code

After reading the [ROS-indgo's installation page](http://wiki.ros.org/indigo/Installation/Ubuntu) I realized that Precise was no longer supported. I therefore had to do an [OS update to Trusty](https://github.com/dnschneid/crouton/wiki/Upgrade-chroot-release). After that a simple installation `$ sudo apt-get install ros-indigo-desktop-full` was able to set me up. 

Don't forget to `$ source /opt/ros/indigo/setup.bash` or echo it into your .bashrc as I scratched my head over `$ roscore` giving me `command not found` for the longest time. 

---

# Other Software 

Originally the thing I was most concerned with was my control over serial or networking ports. As my job surrounds alot around testing firmware 
via USB, I was worried that chromebooks would have some security feature that would lock it up to prevent me from accessing them. But lo and behold: 

<img src="https://www.dropbox.com/s/8agwotmdphbw1eq/Screenshot%202016-03-24%20at%2011.11.53%20PM.png?raw=1" class="img-thumbnail"> 

Also to verify, the permissions carry over in chroot: 

<img src="https://www.dropbox.com/s/7q1h4ccf8m6zhrs/Screenshot%202016-03-24%20at%2011.14.57%20PM.png?raw=1" class="img-thumbnail">

Then I also tested program's abilities to access these ports. Eg. Arduino. A easy blinky test on an uno showed that everything seems to be setup for development. Installing arduino in the chroot, I ran `$ sudo xiwi -F arduino` 

<img src="https://www.dropbox.com/s/0h1y9ainpfhxeuh/Screenshot%202016-03-25%20at%2012.07.50%20AM.png?raw=1" class="img-thumbnail">



