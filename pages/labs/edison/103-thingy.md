---
layout: page-fullwidth
title: "Building the Thingy 4 Edison"
subheadline: "Building Connected Things with Node.js, Johnny-Five, and Microsoft Azure"
teaser: "In this lab, you will use multiple Grove sensors and actuators to build a device with multiple inputs and outputs. You will use this device for the rest of the labs in this series."
show_meta: true
comments: true
header: no
breadcrumb: true
categories: [edison, iot, maker, javascript, node.js, johnny-five]
permalink: /labs/edison/grove/thingy/
---
# Table of Contents
*  Auto generated table of contents
{:toc}

# Setting Up the Board
In this lab, you will use multiple Grove sensors and actuators to build a device with multiple inputs and outputs. You will use this device for the rest of the labs in this series.

1. Connect the Grove shield to the Edison (mounted to the Arduino adapter).
2. Connect the Grove Button Sensor to port __D4__
3. Connect the LED Module to port __D6__, and insert an LED into the LED Module. 
4. Connect the RGB LCD Module to one of the __I2C__ ports.
5. Connect the Temperature Sensor (TH02) to __A0__ port.

# Building the Application
There are three (3) steps to building the application in this lab:

1. Define and install the application metadata and dependencies.
2. Define the device abstractions.
3. Handle the board.on("ready") callback.

The _ThingLabs Thingy&trade; for Edison_ has the following capabilities:

* Capture the ambient temperature and display it on the LCD screen.
* Alter the color of the LCD screen based on the ambient temperature.
* Turn the LED on and off by pressing and releasing the button.

## Define an Install the Application Metadata and Dependencies
Defining and installing the application metadata and dependencies works similarly to steps 1-3 in the [Hello, IoT World!](../hello-iot-world/) lab.

{% include edison/package-generic.md %}

## Define the Device Abstractions
{% include edison/define-thingy1.md %}

## Handle the board.on("ready") Callback
{% include johnny-five/board-on-thingy1.md %}

# Run the Application

1. Copy the application files to the Edison in the same way you have done in the preceeding labs.
2. Install the application dependencies on the Edison (if you are using the same application directory as the preceeding labs, the dependencies should all be installed already). 
3. From the remote session (Command Prompt or Terminal), execute the following command in the application directory. In this lab you are specifying the JavaScript file that node.exe should execute.

{% highlight text %}
node thingy.js
{% endhighlight %}

Once the application is running, you will see the temperature reading on the RGB LCD Display. You can press the button and see the LED light up (you will also see the `PRESSED` log message in the remote session). As long as the button is pressed, the LED will remain on. When you release the button, the `button.on('release')` callback is invoked and the LED turns off (you will also see the `RELEASED` log message). 
  
When you want to quit the application, press <kbd>CTRL</kbd> + <kbd>C</kbd> twice to exit the program without closing the window (you may also have to press <kbd>Enter</kbd>). 

# Conclusion &amp; Next Steps
In this lab, you built the _ThingLabs Thingy&trade; for Edison_, which you will use in the subsequent labs.


{% include edison/nextlab title='Setting Up an Azure IoT Hub' url='../setup-iot-hub/' %}