## Setting up Landzo 3.5" Touch Screens
In recent Raspi projects, it has come to the point where repeatedly plugging and un-plugging of HDMI cables is no longer viable. Hunting of free monitors laying around have also become a hassle that I no longer want to deal with since I enjoy working portably. Wanting to minimize cost for the project, instead of opting for mini hdmi monitors, I found these 3.5" mini monitors that utilize the GPIO ports on the Raspberry-pi's I was working with. Also to minimize cost I opted in buying the cheapest screens I could find at the time. Unfortunate for me, it was cheap enough to not include any paper manuals. 

To the manufacturer's credit, a small CD was included, but with the lack of any laptop with a cd drive, I opted away from them. However, the setup solution was found after digging through the internet for hours. 

The process was challenging primarily due to the fact that there were nock-offs all over the internet which all had different setup instructions. Testing through many setup options from different companies, a script that worked for Landzo was found.

### Landzo Touch Screen 
Some background Information about the Landzo touch screen. The one bought was being sold on [Amazon](https://www.amazon.ca/LANDZO-Touch-Screen-Raspberry-Model/dp/B01IGBDT02). Detailed information can be found on the manufacturer's [website](http://www.landzo.com/index.php?route=product/product&product_id=50). However, as expected there were no setup instructions there. 

From research the setup should not have been as complicated as presented. It would've involved a combination of: 
-Adjusting system output from hdmi to the gpio pins
-Setting up some driver for displaying and another driver for using it as an input device 

Good thing is that someone has written [scripts](https://github.com/goodtft/LCD-show) specfically for setting these monitors up. 

However, I personally prefer a rotated screen, therefore I had to make some small adjustments. Assuming that my Raspberry Pi is a fresh install, I do the following: 

1. Clone the repository and edit `LCD-show/usr/99-calibration.conf-35` to include swapping of the X and Y axis since the screen will be rotated. This is not the rotation of the display, but rather as the file name suggests, it is for calibration of the touch panel. If this was not changed, the touch input will be registered mirrored along the X and Y axis.  
eg:  
>  
Section "InputClass" 
        Identifier      "calibration" 
        MatchProduct    "ADS7846 Touchscreen" 
        Option  "Calibration"   "3936 227 268 3880" 
        Option  "SwapAxes"      "1" 
EndSection 
>

2. Next to apply the visual rotation, we will need to edit `boot/config.txt` and append `:rotate=270` to the `dtoverlay` line. This should look like the following: 
>
dtoverlay=tft35a:rotate=270 
>  

You might notice horizontal display is 270 degrees. 0 and 180 are the vertical orientations. 

3. Reboot

Annnd you'redone setting things up. 






