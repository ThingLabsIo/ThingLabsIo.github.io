---
layout: page-fullwidth
title: "Arduino Labs: Getting Started"
subheadline: "A Step-by-Step Guide"
teaser: "This is a step-by-step guide to preparing your computer for the Arduino Labs."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [arduino, redboard, maker-101]
author: doug_seven
permalink: "/device/arduino/getting-started"
---
### Table of Contents
*  Auto generated table of contents
{:toc}

## Preparing for the Arduino IoT Labs
The labs in this series build on each other to enable you to prototype your own Internet of Things (IoT) devices. In this lab you will build a solution using a 'Gateway' pattern, where a device (the Gateway) collects data from one or more connected devices/sensors and aggregates the data before sending it to the cloud. For the gateway, you will use Node.js and an open source framework for interacting with hardware, called Johnny-Five, which works as a baseline control kit for hardware projects, including the Arduino, Raspberry Pi, and Particle Photon boards. This enables you to write applications in JavaScript that can run as a gateway either on your computer or on a hub device (like an Arduino Y&uacute;n or Raspberry Pi 2) connected to an variety of sensors and devices.

<img src="/images/gatewaypattern.png"/>

If you want to deploy the applications you build to a device to act as the gateway, you will need a device that has a Linux distribution (such as the Raspberry Pi 2, Arduino Y&uacute;n or the Linino ONE).

## Bill of Materials
To prepare your development environment for this lab series you don't need anything other than a computer. Each lab in the series will have a bill of materials indicating what is required for that lab.

If you want to prepare yourself further before the labs you can acquire the following:

1. [SparkFun RedBoard Programmed with Arduino - $19.95](https://www.sparkfun.com/products/12757), [Arduino Uno R3 - $24.95](https://www.sparkfun.com/products/11021), or [Arduino Y&uacute;n - $74.95](https://www.sparkfun.com/products/12053)
2. USB cable to connect the Arduino to your computer (the type will vary depending on the Arduino board you are using)

## Install a Code Editor
If you don't already have one installed, pick a text/code editor. Feel free to use anything you like, provided it won't inject any extra text into your files.

Some Options:

* [Visual Studio Code](http://code.visualstudio.com) (this is our preferred tool)
* [Visual Studio](http://www.visualstudio.com)
* [Sublime Text](http://www.sublimetext.com) 
* [Eclipse](http://www.eclipse.org/downloads/) 
* [Notepad++](http://notepad-plus-plus.org/)

## Install Node.js v0.12.7
In the labs you will write small programs that will run on your computer, connected to your Photon. These programs will be written in JavaScript and will be built on Node.js. If you are not familiar or experienced with Node.js, don't worry. You will learn everything you need to know for these labs in these labs. 

Follow the [instructions here to install Node.js][nodejs] on your computer.  __IMPORTANT:__ Be sure to install [version 0.12.x][node_12_7] - not version 4.0.

## Install Johnny-Five
Johnny-Five is an open source JavaScript framework that provides a simple object model for interacting with an Arduino-based board and the sensors and devices you connect to it. 

Once you have Node.js installed, install [Johnny-Five][j5] using NPM. On Windows, open the Node.js command prompt and type the following:
<pre>
npm install -g johnny-five
</pre>

On Mac OS X open Terminal and type the following:
<pre>
sudo npm install -g johnny-five
</pre>

## Set Up a Development Directory
The last thing to do is prepare a place to save all of your work in the labs. We recommend an easy to navigate to directory with a relatively short path. Create a new folder/directory for the labs - I recommend:

### Windows
<pre>
C:\Development\IoTLabs
</pre>

### Mac OS X
<pre>
~/Devleopment/IoTLabs
</pre>

That's it for now. You are ready to start the [first set of labs][nextlab].

{% include next-previous-post-in-category.html %}

[nodejs]: http://nodejs.org/
[npmj5]: http://www.johnny-five.io
[node_12_7]: https://nodejs.org/dist/v0.12.7/
[nextlab]: setup-arduino