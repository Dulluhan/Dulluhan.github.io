## Setting up Landzo 3.5" Touch Screens
In recent Raspi projects, it has come to the point where repeatedly plugging and un-plugging of HDMI cables is no longer viable. Hunting of free monitors laying around have also become a hassle that I no longer want to deal with since I enjoy working portably. Wanting to minimize cost for the project, instead of opting for mini hdmi monitors, I found these 3.5" mini monitors that utilize the GPIO ports on the Raspberry-pi's I was working with. Also to minimize cost I opted in buying the cheapest screens I could find at the time. Unfortunate for me, it was cheap enough to not include any paper manuals. 

To the manufacturer's credit, a small CD was included, but with the lack of any laptop with a cd drive, I opted away from them. However, the setup solution was found after digging through the internet for hours. 

The process was challenging primarily due to the fact that there were nock-offs all over the internet which all had different setup instructions. Testing through many setup options from different companies, a script that worked for Landzo was found.

### Landzo Touch Screen 
Some background Information about the Landzo touch screen. The one bought was being sold on [Amazon](https://www.amazon.ca/LANDZO-Touch-Screen-Raspberry-Model/dp/B01IGBDT02). Detailed information can be found on the manufacturer's [website](http://www.landzo.com/index.php?route=product/product&product_id=50). However, as expected there were no setup instructions there. 

From research the setup should not have been as complicated as presented. It would've involved a combination of: 
-Adjusting system output from hdmi to the gpio pins
-Setting up some driver for displaying and another driver for using it as an input device
