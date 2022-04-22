[< HOME](../README.md)
# piTouchMini

## Parts List
1. pi 4 4GB
2. Touch screen and case. [on Amazon](https://www.amazon.com/gp/product/B07WQW6H9S/ref=ppx_yo_dt_b_search_asin_title?ie=UTF8&psc=1)

# Setup
## Install drivers
1. SSH into pi
2. sudo rm -rf LCD-show
3. git clone https://github.com/goodtft/LCD-show.git
4. chmod -R 755 LCD-show
5. cd LCD-show
6. sudo ./MHS35-show

## How to switch to HDMI ?
if need change to back HDMI monitor, (HDMI Screen and 3.5 inch screen can't work at same time), please execute:
1. cd LCD-show/
2. sudo ./LCD-hdmi

## How to rotate the screen ?
1. cd LCD-show/
2. sudo ./rotate.sh 90

After the execution is completed, the system will automatically restart, then the display can be rotated 90 degrees to display and touch normally. (' 90 ' can also be changed to 0, 90, 180, 270 and other values, representing the rotation angle of 0 degrees, 90 degrees, 180 degrees, 270 degrees)

# Notes
1. HDMI ports dont work when touch screen active
2. After the screen is installed, GPIO cannot be used. And Don't have backlight ON/Off Key
3. After install the driver according Tutorial Step, some customers will meet the system crashes or can display nothing on the screen (If you are not familiar with the Raspberry Pi, We strongly recommended you that use the system with the driver installed)
4. The resolution is small, only 320*480 P, the screen often cannot display the complete interface.
5. Customers often have wrong connection screen with Pi board, Led to the touch function can not be used
6. Does not support other systems such as Windows.
7. It's not IPS screen, The best view able is in the vertical position