---
layout: page-js
title: "Getting Started"
subheadline: "Building Connected Things with JavaScript"
teaser: "This is a step-by-step guide to preparing your computer for the Arduino Labs."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [arduino, photon, iot, maker, javascript, node.js, johnny-five]
permalink: /lang/js/getting-started/
---
### Table of Contents
*  Auto generated table of contents
{:toc}

## Preparing for the IoT Labs
The labs in this series build on each other to enable you to prototype your own Internet of Things (IoT) devices. In this lab you will build a solution using a 'Gateway' pattern, where a device (the Gateway) collects data from one or more connected devices/sensors and aggregates the data before sending it to the cloud. For the gateway, you will use Node.js and an open source framework for interacting with hardware, called Johnny-Five, which works as a baseline control kit for hardware projects, including the Particle Photon, Arduino and Raspberry Pi boards. This enables you to write applications in JavaScript that can run as a gateway either on your computer or on a hub device (like a Raspberry Pi 2) connected to an variety of devices.

<img src="/images/gatewaypattern.png"/>

## Bill of Materials
To prepare your development environment for this lab series you don't need anything other than a computer. Each lab in the series will have a bill of materials indicating what is required for that lab.

If you want to prepare yourself further before the labs you can acquire the following:

1. One of the following development boards:
  * [SparkFun RedBoard Programmed with Arduino](https://www.sparkfun.com/products/12757)
  * [Arduino Uno R3](https://www.sparkfun.com/products/11021)
  * [Arduino Y&uacute;n](https://www.sparkfun.com/products/12053)
  * [Particle Photon Development Kit](https://store.particle.io/?product=photon-kit)
2. USB cable to connect the board to your computer (the type will vary depending on the board you are using)
3. [5mm Green LED](http://www.sparkfun.com/products/12062)
4. [330 Ohm 1/4 Watt resistor](http://www.sparkfun.com/products/10969) (Orange-Orange-Brown)
5. [Photoresistor (5528)](http://www.sparkfun.com/products/9088)
6. [10k Ohm 1/4 Watt resistor](http://www.sparkfun.com/products/10969) (Brown-Black-Orange)

Here are a couple of SparkFun Wish Lists that you can choose from if you need gear:

<script src="https://www.sparkfun.com/wish_lists/120919.js"></script>

<script src="https://www.sparkfun.com/wish_lists/120932.js"></script>

<script src="https://www.sparkfun.com/wish_lists/120980.js"></script>

## Install a Code Editor
If you don't already have one installed, pick a text/code editor. Feel free to use anything you like, provided it won't inject any extra text into your files.

Some Options:

* [Visual Studio Code](http://code.visualstudio.com) (this is our preferred tool)
* [Visual Studio](http://www.visualstudio.com)
* [Sublime Text](http://www.sublimetext.com) 
* [Eclipse](http://www.eclipse.org/downloads/) 
* [Notepad++](http://notepad-plus-plus.org/)

## Install Node.js v0.12.7
In the labs you will write small programs that will run on your computer, connected to your board. These programs will be written in JavaScript and will be built on Node.js. If you are not familiar or experienced with Node.js, don't worry. You will learn everything you need to know for these labs in these labs. 

Follow the [instructions here to install Node.js](http://www.nodejs.org) on your computer.  __IMPORTANT:__ Be sure to install [version 0.12.x][node_12_7] - not version 4.0.

## Install Johnny-Five
Johnny-Five is an open source JavaScript framework that provides a simple object model for interacting with a variety of hardware boards and the sensors and devices you connect to them. 

Once you have Node.js installed, install [Johnny-Five][11] using NPM.
On Windows, open the Node.js command prompt and type the following:
<pre>
  npm install -g johnny-five
</pre>

On Mac OS X open Terminal and type the following:
<pre>
  sudo npm install -g johnny-five
</pre>

## Set Up a Development Directory
The last thing to do is prepare a place to save all of your work in the labs. I recommend an easy to navigate to directory with a relatively short path. Create a new folder/directory for the labs - I recommend:

### Windows
<pre>
  C:\Development\IoTLabs
</pre>

### Mac OS X
<pre>
  ~\Development\IoTLabs
</pre>

## Create a Microsoft Azure Trial Account
In this lab series you will use Microsoft Azure as the cloud backend for your IoT solution. If you don't already have an Azure account, go to [https://azure.microsoft.com/en-us/pricing/free-trial/][15] to start a free trial of Microsoft Azure. You may need a credit card for identity verification, but the trial is completely free. If you have an MSDN Subscription you may be eligible for free credits to Microsoft Azure every month. Check your [MSDN account][16] page for details.

# Conclusion &amp; Next Steps
In this lab you prepared your development machine for the following labs. The next step is to configure your board to become a connected Thing. How you do this will depend on the board you are using.

Choose your own adventure - click one of these options to configure your board.

<a class="radius button small" href="{{ site.url }}/lang/js/setup-arduino/">Go to 'Setting Up Your Arduino' ›</a>

<a class="radius button small" href="{{ site.url }}/lang/js/setup-photon/">Go to 'Setting Up Your Particle Photon' ›</a>

 [1]: https://store.particle.io/?product=photon-kit
 [3]: http://www.particle.io/prototype#cli
 [4]: http://code.visualstudio.com
 [5]: http://www.visualstudio.com 
 [6]: http://www.sublimetext.com 
 [7]: http://www.eclipse.org/downloads/ 
 [8]: http://notepad-plus-plus.org/
 [9]: http://git-scm.com/
 [10]: http://nodejs.org/
 [11]: http://www.npmjs.com/package/johnny-five
 [12]: http://www.nitrogen.io
 [14]: https://build.particle.io/signup
 [15]: https://azure.microsoft.com/en-us/pricing/free-trial/
 [16]: https://msdn.microsoft.com/subscriptions/manage/
 [node_12_7]: https://nodejs.org/dist/v0.12.7/