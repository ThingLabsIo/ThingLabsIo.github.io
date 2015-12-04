---
layout: page-fullwidth
title: "Setting Up Your Arduino Firmware"
subheadline: "Arduino Thing 101 Labs #1"
teaser: "In this lab you will install firmware onto your Arduino that will enable communication with the gateway application."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [arduino, iot, maker, javascript, node.js, johnny-five]
author: doug_seven
permalink: "/device/arduino/setup-arduino"
---
### Table of Contents
*  Auto generated table of contents
{:toc}

If you haven't already done so, please follow the instructions in [Lab 00: Getting Started](../getting-started) section.

In this lab you will install the Standard Firmatta onto your Arduino. The Standard Firmatta exposes basic input/output on the device for your gateway application to access.

## Bill of Materials
What you will need:

1. [SparkFun RedBoard Programmed with Arduino - $19.95](https://www.sparkfun.com/products/12757), [Arduino Uno R3 - $24.95](https://www.sparkfun.com/products/11021), or [Arduino Y&uacute;n - $74.95](https://www.sparkfun.com/products/12053)
2. USB cable to connect the Arduino to your computer (the type will vary depending on the Arduino board you are using)

## Install the Arduino IDE
While you won't use the Arduino IDE very much in the set of labs, it is necessary for a couple of things. For one thing, installing the Arduino IDE also installs the USB drivers for the Arduino board.

Go to [http://www.arduino.cc][arduino] and follow the links to download the latest version of the Arduino IDE. Make sure that the checkbox for the USB driver is selected during install (it typically is by default).

## Upload the Standard Firmata to your Arduino
For this lab series you will use tools that require your Arduino to have the Standard Firmatta loaded. Fortunately, this is easy to do using the Arduino IDE.

1. Connect your Arduino board via USB to your computer.
2. Open the Arduino IDE.
3. Using the _Tools_ menu, select _Board_ and make sure the type of board you are using (Arduino Uno or Arduino Y&uacute;n) is selected.
4. Using the _Tools_ menu, select _Port_ and make sure your board is selected on a COM port.
5. Using the _File_ menu, select _Examples_ > _Firmata_ > _StandardFirmata_. This will open a new Arduino IDE window with the code for the Standard Firmata loaded.
6. Using the _Sketch_ menu, select _Upload_. This will upload the firmware to your Arduino (you should see the lights on the Arduino blink while this happens).
7. Close all Arduino IDE windows.

## Conclusion &amp; Next Steps
Congratulations! You have configured a physical device, a software representation of that device, and the IoT Hub service to connect it to. In the [next lab][nextlab] you will build a Thing that is controlled by the gateway.

[Next Lab ->][nextlab]

{% include next-previous-post-in-category.html %}

[uno]: http://www.arduino.cc/en/Main/ArduinoBoardUno
[yun]: http://www.arduino.cc/en/Main/ArduinoBoardYun
[arduino]: http://www.arduino.cc
[nextlab]: writing-digital-output