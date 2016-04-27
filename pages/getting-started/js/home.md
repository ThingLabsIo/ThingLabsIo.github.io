---
layout: page-fullwidth
title: "Getting Started with Node.js and Johnny Five"
subheadline: "Building Connected Things with Node.js, Johnny-Five, and Microsoft Azure"
teaser: "This is a step-by-step guide to preparing your computer for labs that use Node.js, Johnny-Five, and Microsoft Azure IoT."
show_meta: true
comments: true
header: no
breadcrumb: true
categories: [edison, bbg, arduino, photon, iot, maker, javascript, node.js, johnny-five]
permalink: /getting-started/js/
---
# Table of Contents
*  Auto generated table of contents
{:toc}

Many of the labs, tutorials, and workshops on ThingLabs.io use Node.js and an open source framework named [Johnny-Five](http://johnny-five.io){:target="_blank"}. If you will be going through any of these labs, tutorials, or workshops you need to first prepare your development environment. This lab is meant to be done before any other labs, tutorials, or workshops to ensure your development environment is ready.

# Requirements
The labs, tutorials, and workshops that use Windows 10 IoT depend on the following:

* An editor for writing JavaScript files.
* An SSH utility.
* A file transfer utility.
* A Microsoft Azure account.

# Install a Code Editor
If you don't already have one installed, pick a text/code editor. Feel free to use anything you like, provided it won't inject any extra text into your files.

Some Options:

* [Visual Studio Code][vscode]{:target="_blank"} (this is our preferred tool)
* [Visual Studio][vs]{:target="_blank"}
* [Sublime Text][sublime]{:target="_blank"}
* [Eclipse][eclipse]{:target="_blank"}
* [Notepad++][notepad]{:target="_blank"}

# Install PuTTy  (Windows only)
PuTTY is an SSH and telnet client for the Windows platform. Mac OS X develoeprs will use the built-in `screen` utility.

1. Install [PuTTy](http://www.putty.org/){:target="_blank"}

# Install a File Transfer Utility
in the labs you will be coding on your development machine and then transferring the code files to the prototyping board to run the application on the _Thing_. One of the easiest ways to do this is with a file transfer utility. _ThongLabs Tinkerers_ recommend _FileZilla_ - it is an easy to use, drag-n-drop file transfet utility. 

1. Using a browser, navigate to the [FileZilla Progect website](https://filezilla-project.org/){:target="_blank"}.
2. Follow the links and instructions to __Download FileZilla Client__.
 
# Create a Microsoft Azure Trial Account
In this lab series you will use Microsoft Azure as the cloud backend for your IoT solution. If you don't already have an Azure account, go to [https://azure.microsoft.com/en-us/pricing/free-trial/][azure]{:target="_blank"} to start a free trial of Microsoft Azure. You may need a credit card for identity verification but the trial is completely free. If you have an MSDN Subscription you may be eligible for free credits to Microsoft Azure every month. Check your [MSDN account][msdn]{:target="_blank"} page for details.

# Conclusion &amp; Next Steps
In this lab you prepared your development environment for labs, tutorials, and workshops on __ThingLabs.io__ that use Node.js and Johnny-Five. The next step is to configure your prototypong board and begin building a _Thing_. 

The following prototyping boards have labs, tutorials, or workshops on ThingLabs.io. Select your board and continue on to configure it. 

## Microcontrollers Running Real-Time Operating Systems (RTOS) 

<a class="radius button small" href="{{ site.url }}/getting-started/js/arduino/">Go to 'Setting Up Your Arduino' ›</a>

<a class="radius button small" href="{{ site.url }}/getting-started/js/photon/">Go to 'Setting Up Your Particle Photon' ›</a>

<a class="radius button small" href="{{ site.url }}/getting-started/js/huzzah-esp8266/">Go to 'Setting Up Your AdaFruit Huzzah Feather (ESP8266)' ›</a>

## Microprocessors Running Linux

<a class="radius button small" href="{{ site.url }}/getting-started/js/edison/">Go to 'Setting Up Your Intel Edison' ›</a>

<a class="radius button small" href="{{ site.url }}/getting-started/js/rpi/">Go to 'Setting Up Your Raspberry Pi' ›</a>

<a class="radius button small" href="{{ site.url }}/getting-started/js/beagle/">Go to 'Setting Up Your BeagleBone' ›</a>

[vscode]: http://code.visualstudio.com
[vs]: http://www.visualstudio.com 
[sublime]: http://www.sublimetext.com 
[eclipse]: http://www.eclipse.org/downloads/ 
[notepad]: http://notepad-plus-plus.org/
[node]: http://nodejs.org/
[azure]: https://azure.microsoft.com/en-us/pricing/free-trial/
[msdn]: https://msdn.microsoft.com/subscriptions/manage/
