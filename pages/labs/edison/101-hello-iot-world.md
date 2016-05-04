---
layout: page-fullwidth
title: "Hello, IoT World!"
subheadline: "Building Connected Things with Node.js, Johnny-Five, and Microsoft Azure"
teaser: "In this lab, you will use an LED Module to send digital output in the form of a blinking LED. This is the Hello, World of the IoT space."
show_meta: true
comments: true
header: no
breadcrumb: true
categories: [edison, iot, maker, javascript, node.js, johnny-five]
permalink: /labs/edison/js/grove/hello-iot-world/
---
# Table of Contents
*  Auto generated table of contents
{:toc}

# Setting Up the Board
For this lab, you will create a _Thing_ that can blink an LED. 

1. Connect the Grove shield to the Edison (mounted to the Arduino adapter).

# Building the Application
There are five (5) steps to building the application in this lab:

1. Define the application metadata and dependencies.
2. Install the dependencies.
3. Install specific dependencies for the Intel Edison.
4. Define the device abstractions.
5. Handle the board.on("ready") callback.

## Define Application Metadata and Dependencies
{% include node/node-package.md %}

## Install Dependencies Using NPM
{% include node/install-dependencies.md %}

## Install Specific Dependencies for the Intel Edison Board.
{% include edison/edison-io.md %}

## Define the Device Abstractions
{% include edison/define-blinky.md %}

## Handle the board.on("ready") Callback
{% include johnny-five/board-on-blinky.md %}

# Run the Application
The Blinky application will run on the Intel Edison, but first, you have to get the code on the board. 

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
   * Right-click on __root__ and select __Create directory__
   * Name the directory __labs__
9. Drag the __package.json__ and __blinky.js__ files to the _labs_ directory on the Edison.

## Install the Application Dependencies
You can remotely execute NPM on the board to install the application dependencies. The instructions are a little different, depending on whether you are using a Windows PC or a Mac. Select the appropriate tab below and follow the instructions.

{% include edison/serial-screen.md %}

1. To install the application dependencies on the Edison, navigate to the directory where the application files are located and run `npm install` by executing the following commands:
{% highlight text %}
cd labs
npm install
{% endhighlight %}

## Run the Application on the Board
From the remote session (Command Prompt or Terminal), execute the following command in the application directory (remember, using `.` will tell node.exe to execute the JavaScript file referenced in the `main` property of the package.json file):

{% highlight text %}
node .
{% endhighlight %}

You should the indicator LED on the Arduino shield start to blink once per second.
  
When you want to quit the application, press <kbd>CTRL</kbd> + <kbd>C</kbd> twice to exit the program without closing the window (you may also have to press <kbd>Enter</kbd>). 

# Conclusion &amp; Next Steps
In this lab, you learned how to write a Node.js/Johnny-Five application that writes LOW (0) and HIGH (1) signals to a digital output pin to make an LED blink. In itself, this may not be very exciting, but the core concept is necessary.

In this lab you learned the following concepts:

1. Creating a Node.js application using Johnny-Five.
2. Working with digital output.
3. Running the application on a device. 

{% include edison/nextlab title='Responding to Sensor Input' url='../respond-to-sensor/' %}