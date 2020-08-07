# RaspberryPi-GPIO-Displays

A recent kernel update to version 5.4, changed the GPIO configuration for a number of small directly connected SPI LCD displays for the Raspberry Pi.
The reason seems to be that GPIO descriptors have been changed from pin numbers to labels.

I have tried to use an Waveshare 3.5" 480x320 ILI9486 display with the Waveshare driver, and also with two other drivers, without sucess with the new kernel. Note that all these drivers work without problems with the previous 4.19 kernel.

I decided to try a new approach, and use the FBCP SPI driver at [**Kpishere**](https://github.com/kpishere/fbcp-ili9341). This driver does not use the notro/fbtft framebuffer driver, i.e. lines such as dtoverlay=waveshare35xx should be removed from /boot/config.txt. This program also does not use the default SPI driver, so a line such as dtparam=spi=on in /boot/config.txt should also be removed. Similarly, if there are touch controller related dtoverlays active, such as dtoverlay=ads7846 those should be removed. The driver has its own touch screen driver.

**I used the following to make the driver:**

    $ sudo apt-get install cmake
    $ git clone https://github.com/juj/fbcp-ili9341.git
    $ cd fbcp-ili9341
    $ mkdir build
    $ cmake -DSPI_BUS_CLOCK_DIVISOR=30 -DWAVESHARE35B_ILI9486=ON -DBACKLIGHT_CONTROL=ON -DDISPLAY_INVERT_COLORS=ON -DSTATISTICS=0 ..
    $ make -j
    $ cd fbcp-ili9341
    $ sudo ./fbcp-ili9341
    
**Also add the following lines in /boot/config.txt:**

    $ hdmi_group=2
    $ hdmi_mode=87
    $ hdmi_cvt=480 320 60 1 0 0 0
    $ hdmi_force_hotplug=1

**To start at boot, edit the /etc/rc.local in sudo mode, and add a line:**

    $ sudo /home/pi/fbcp-ili9341/build/fbcp-ili9341 &
    
The result surprised me in the responsiveness I obtained, compared when using the same display with the Waveshare driver with kernel 4.19
I intend to test another display from waveshare, and test it using a Raspberyy Pi 4. 

The first image is the result of the first run of the driver, and tries to run the LCD display at 1920x1080 pixels. The second, and fourth picture, is after the cmake line: cmake -DSPI_BUS_CLOCK_DIVISOR=30 -DWAVESHARE35B_ILI9486=ON .. was used to build the driver, and shows inverted colours. The thisrd picture and the fifth picture is with the corrected colours.
<br>
<p align="center">
<img src="images/Pi3BPWave35Bv2-1.jpg" width="300" />  
<img src="images/Pi3BPWave35Bv2-3.jpg" width="300" />  
<img src="images/Pi3BPWave35Bv2-5.jpg" width="300" />  
<br>
    
    
<br>
<p align="center">
<img src="images/Pi3BPWave35Bv2-2.jpg" width="400" />  
<img src="images/Pi3BPWave35Bv2-4.jpg" width="400" />  
<br>

