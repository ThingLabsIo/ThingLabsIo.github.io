---
layout: page-fullwidth
title: "Microsoft Azure IoT Labs - Getting Started"
subheadline: "A Step-by-Step Guide"
teaser: "This is a step-by-step guide to preparing your computer for the Azure IoT Labs."
show_meta: true
comments: true
header: no
breadcrumb: true
categories:
  - azure
  - iot-labs
  - maker-101
author: doug_seven
permalink: "/getting-started/"
---
### Table of Contents
*  Auto generated table of contents
{:toc}

## Preparing for the Azure IoT Labs
The labs in this series build on each other to enable you to prototype your own Internet of Things (IoT) devices using Microsoft Azure. In this lab you will use Node.js and an open source framework for interacting with hardware called Johnny Five, which works as a baseline control kit for hardware projects, including the Arduino, Particle Photon, and Raspberry Pi boards. This enables you to write applications in JavaScript that can run either on your computer or on a hub device (like an Arduino Y&uacute;n or Raspberry Pi) connected to an variety of devices.

The first part of this lab series will teach you how to create IoT devices that send telemetry to the cloud. You will use Azure services, such as Stream Analytics and PowerBI to create visualizations of the telemetry. In the second part of the lab series you will use Notification Hubs to send command &amp; control messages back to the device.

## Bill of Materials
To prepare your development environment for this lab series you don't need anything other than a computer. Each lab in the series will have a bill of materials indicating what is required for that lab.

## Install a Code Editor
If you don't already have one installed, pick a text/code editor. Feel free to use anything you like, provided it won't inject any extra text into your files.

Some Options:

* [Visual Studio Code][vscode] (this is our preferred tool)
* [Visual Studio][vs]
* [Sublime Text][sublime] 
* [Eclipse][eclipse] 
* [Notepad++][notepad]

## Install Node.js (v0.12.x)
In the labs you will write small programs that will run on your computer, connected to your _Thing_. These programs will be written in JavaScript and will be built on Node.js. If you are not familiar or experienced with Node.js, don't worry. You will learn everything you need to know for these labs in these labs. 

Follow the [instructions here to install Node.js][node] on your computer. __Be sure to install [version 0.12.x][node_12_7]__ - not version 4.0.

## Install Johnny-Five
Johnny-Five is an open source JavaScript framework that provides a simple object model for interacting with a variety of hardware boards and the sensors and devices you connect to them. 

Once you have Node.js installed, install [Johnny-Five][j5] using NPM.
On Windows, open the Node.js command prompt and type the following:
<pre>
  npm install -g johnny-five
</pre>

On Mac OS X open Terminal and type the following:
<pre>
  sudo npm install -g johnny-five
</pre>

## Set Up a Development Directory
Prepare a place to save all of your work in the labs. We recommend an easy to navigate to directory with a relatively short path. Create a new folder/directory for the labs:

### Windows
<pre>
  C:\Development\IoTLabs
</pre>

### Mac OS X
<pre>
  ~/Development/IoTLabs
</pre>

## Create a Microsoft Azure Trial Account
In this lab series you will use Microsoft Azure as the cloud backend for your IoT solution. If you don't already have an Azure account, go to [https://azure.microsoft.com/en-us/pricing/free-trial/][azure] to start a free trial of Microsoft Azure. You may need a credit card for identity verification but the trial is completely free. If you have an MSDN Subscription you may be eligible for free credits to Microsoft Azure every month. Check your [MSDN account][msdn] page for details.

## Conclusion &amp; Next Steps
That's it for now. You are ready to start the [first set of labs][nextlab].

{% include next-previous-post-in-category.html %}

[getting-started]: /getting-started
[setup-azure-iot-hub]: /setup-azure-iot-hub
[setup-photon]: /setup-photon
[setup-arduino]: /setup-arduino
[sending-telemetry]: /sending-telemetry
[visualize-iot-with-powerbi]: /visualize-iot-with-powerbi
[nextlab]: /setup-azure-iot-hub/
[vscode]: http://code.visualstudio.com
[vs]: http://www.visualstudio.com 
[sublime]: http://www.sublimetext.com 
[eclipse]: http://www.eclipse.org/downloads/ 
[notepad]: http://notepad-plus-plus.org/
[git]: http://git-scm.com/
[node]: http://nodejs.org/
[j5]: http://www.npmjs.com/package/johnny-five
[azure]: https://azure.microsoft.com/en-us/pricing/free-trial/
[msdn]: https://msdn.microsoft.com/subscriptions/manage/
[node_12_7]: https://nodejs.org/dist/v0.12.7/