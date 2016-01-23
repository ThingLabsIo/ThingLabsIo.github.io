---
layout: page-fullwidth
title: "Connected Weather Station - Getting Started"
subheadline: "Microsoft Azure IoT Connected Weather Station Project"
teaser: "This is a step-by-step guide to preparing your computer for the Connected Weather Station labs."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [arduino, photon, iot, maker, javascript, node.js, johnny-five, weather-station]
author: doug_seven
permalink: /lang/js/weather-station/
---
### Table of Contents
*  Auto generated table of contents
{:toc}

# Preparing for the Connected Weather Station Labs
This lab series will teach you how to build a simple Cloud-connected weather station that will send temperature and barometric pressure data to the Cloud. You will use Microsoft Power BI to create reports on the data. 

# The IoT Gateway Pattern
In this lab you will build a solution using a 'Gateway' pattern, where a device (the Gateway) collects data from one or more connected devices/sensors and aggregates the data before sending it to the cloud. For the gateway, you will use Node.js and Johnny-Five &emdash; an open source framework for interacting with hardware which works as a baseline control kit for hardware projects, including Arduino, Particle Photon, Raspberry Pi, Beagle Bone, and Intel Edison boards. This enables you to write applications in JavaScript that can run as a gateway either on your computer or on a hub device (like a Raspberry Pi 2 or Arduino Y&uacute;n) connected to an variety of devices.

<img src="/images/gatewaypattern.png"/>

# Bill of Materials
To prepare your development environment for this lab series you don't need anything other than a computer. The following labs will require some hardware. You can choose between the Arduino Uno, SparkFun RedBoard, or the Particle Photon. 

You can use one of the following SparkFun Wish Lists (choose only one):

<script src="https://www.sparkfun.com/wish_lists/120920.js"></script>

__OR__  

<script src="https://www.sparkfun.com/wish_lists/121094.js"></script>

__OR__

<script src="https://www.sparkfun.com/wish_lists/120921.js"></script>

# Install a Code Editor
If you don't already have one installed, pick a text/code editor. Feel free to use anything you like, provided it won't inject any extra text into your files.

Some Options:

* [Visual Studio Code][vscode] (this is our preferred tool)
* [Visual Studio][vs]
* [Sublime Text][sublime] 
* [Eclipse][eclipse] 
* [Notepad++][notepad]

# Install Node.js (v0.12.x)
In the labs you will write small programs that will run on your computer, connected to your _Thing_. These programs will be written in JavaScript and will be built on Node.js. If you are not familiar or experienced with Node.js, don't worry. You will learn everything you need to know for these labs in these labs. 

Follow the [instructions here to install Node.js][node] on your computer. __Be sure to install [version 0.12.x][node_12_7]__ - not version 4.0.

# Set Up a Development Directory
Prepare a place to save all of your work in the labs. We recommend an easy to navigate to directory with a relatively short path. Create a new folder/directory for the labs:

## Windows
<pre>
  C:\Development\IoTLabs
</pre>

## Mac OS X
<pre>
  ~/Development/IoTLabs
</pre>

# Create a Microsoft Azure Trial Account
In this lab series you will use Microsoft Azure as the cloud backend for your IoT solution. If you don't already have an Azure account, go to [https://azure.microsoft.com/en-us/pricing/free-trial/][azure] to start a free trial of Microsoft Azure. You may need a credit card for identity verification but the trial is completely free. If you have an MSDN Subscription you may be eligible for free credits to Microsoft Azure every month. Check your [MSDN account][msdn] page for details.

# Conclusion &amp; Next Steps
In this lab you prepared your development machine for the following labs. Next you will set up the Microsoft Azure IoT Hub that will be the Cloud back-end for your project.

<a class="radius button small" href="{{ site.url }}/lang/js/weather-station/setup-azure-iot-hub/">Go to 'Setting Up Azure IoT Hub' ›</a>

[getting-started]: /getting-started/
[setup-azure-iot-hub]: /setup-azure-iot-hub/
[setup-photon]: /setup-photon/
[setup-arduino]: /setup-arduino/
[sending-telemetry]: /sending-telemetry/
[visualize-iot-with-powerbi]: /visualize-iot-with-powerbi/
[nextlab]: /setup-azure-iot-hub/
[vscode]: http://code.visualstudio.com
[vs]: http://www.visualstudio.com 
[sublime]: http://www.sublimetext.com 
[eclipse]: http://www.eclipse.org/downloads/ 
[notepad]: http://notepad-plus-plus.org/
[node]: http://nodejs.org/
[azure]: https://azure.microsoft.com/en-us/pricing/free-trial/
[msdn]: https://msdn.microsoft.com/subscriptions/manage/
[node_12_7]: https://nodejs.org/dist/v0.12.7/