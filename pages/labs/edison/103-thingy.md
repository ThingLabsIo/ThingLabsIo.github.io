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
permalink: /labs/edison/js/grove/thingy/
---
# Table of Contents
*  Auto generated table of contents
{:toc}

# Setting Up the Board
In this lab, you will use multiple Grove sensors and actuators to build a device with multiple inputs and outputs. You will use this device for the rest of the labs in this series.

1. Connect the Grove shield to the Edison (mounted to the Arduino adapter).
2. Connect the Grove Button Sensor to port __D4__
3. Connect the LED Module to port __D6__, and insert an LED into the LED Module. 
4. Connect the RGB LSD Module to one of the __I2C__ ports.
5. Connect the Temperature Sensor (TH02) to one of the __I2C__ ports.

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
The Button-Led application will run on the Intel Edison, but first, you have to get the code on the board. 

## Copy the Application Files to the Edison
The application will execute on the Intel Edison.

1. Open __FileZilla__
2. Set the __Host__ to the IP address of your device.
3. Set the __Username__ to the administrative username (e.g. root).
4. Set the __Password__ to whatever you set the password for the device.
5. Set the __Port__ to `22`
6. Click __Quickconnect__
7. In the __Local site__ window (typically the left side), navigate to your local application directory.
8. In the __Remote site__ windows (typically on the right side) - this is the file system on the Edison.
   * Navigate to the __labs__ directory you previously created (e.g. _$root\labs_)
9. Drag the __package.json__ and __button-led.js__ files to the _blinky_ directory on the Edison.

## Install the Application Dependencies
You can remotely execute NPM on the board to install the application dependencies. The instructions are a little different, depending on whether you are using a Windows PC or a Mac. Select the appropriate tab below and follow the instructions.

{% include edison/serial-screen.md %}

## Run the Application on the Board
From the remote session (Command Prompt or Terminal), execute the following command in the application directory. In this lab you are specifying the JavaScript file that node.exe should execute.

{% highlight text %}
node button-led.js
{% endhighlight %}

Once the application is running, you can press the button and see the LED light up (you will also see the `PRESSED` log message in the remote session). As long as the button is pressed, the LED will remain on. When you release the button, the `button.on('release')` callback is invoked and the LED turns off (you will also see the `RELEASED` log message). 
  
When you want to quit the application, press <kbd>CTRL</kbd> + <kbd>C</kbd> twice to exit the program without closing the window (you may also have to press <kbd>Enter</kbd>). 

# Conclusion &amp; Next Steps
In this lab, you learned how to write a Node.js/Johnny-Five application that writes LOW (0) and HIGH (1) signals to a digital output pin to make an LED blink. In itself, this may not be very exciting, but the core concept is necessary.

In this lab you learned the following concepts:
