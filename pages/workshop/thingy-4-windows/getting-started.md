---
layout: "page-fullwidth"
title: "Thingy 4 Windows IoT Workshop: Getting Started"
subheadline: "Building Connected Things with Windows 10 IoT and Microsoft Azure"
teaser: "This is a step-by-step guide to preparing your computer for the Thingy for Windows IoT Workshop."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [raspberry-pi, windows-10, dragonboard, minnowboard, c#, iot, maker]
permalink: /workshop/thingy-4-windows/getting-started/
---
# Table of Contents
*  Auto generated table of contents
{:toc}

# Preparing for the Windows 10 IoT Labs
The labs in this workshop build on each other to enable you to prototype your own Internet of Things (IoT) devices. In this lab you will use the Microsoft .NET Framework to build an application for the Universal Windows Platform (UWP) that can run on any Windows 10 device, including a Raspberry Pi 2, MinnowBoard MAX, or DragonBoard 410c running Windows 10 IoT Core. We will use the Raspberry Pi 2 for this workshop.

# Bill of Materials
In this workshop you will need the following:

1. [Raspberry Pi 2](http://www.amazon.com/Raspberry-Pi-Model-Project-Board/dp/B00T2U7R7I/)
2. [GrovePi+ Starter Kit for Raspberry Pi](http://www.seeedstudio.com/depot/GrovePi-Starter-Kit-for-Raspberry-Pi-ABB23-CE-certified-p-2572.html)
3. [5V (2A to 3A) Switching Power Supply w/ MicroUSB Cable](http://www.amazon.com/CanaKit-Raspberry-Supply-Adapter-Charger/dp/B00MARDJZ4/)
4. A Wi-Fi Adapter (choose one from the list [here](http://ms-iot.github.io/content/en-US/win10/SupportedInterfaces.htm#WiFi-Dongles))
5. 8GB micro SD card - class 10 or better. Microsoft suggests one of the following:
	* [Samsung 32GB EVO Class 10 Micro SDHC up to 48MB/s with Adapter (MB-MP32DA/AM)](http://www.amazon.com/gp/product/B00IVPU786)
	* [SanDisk Ultra Micro SDHC, 16GB Card](http://www.amazon.com/SanDisk-Ultra-Micro-SDHC-16GB/dp/9966573445).


**Note:** The last 3 items are all included in the [Microsoft IoT Pack for Raspberry Pi 2](http://www.adafruit.com/windows10iotpi2){:target="_blank"} from AdaFruit.

# Install Visual Studio 2015
If you don't already have it installed, install [Visual Studio 2015](https://www.visualstudio.com/){:target="_blank"}. You can use the free [Community Edition](https://www.visualstudio.com/post-download-vs/?sku=community&clcid=0x409&telem=ga), or any other higher edition. When you are installing Visual Studio, you must do a __Custom__ install and select to install the __Universal Windows App Development Tools -> Tools and Windows SDK__.  

<img src="/images/rpi2/rpi2_install_uwp.png"/>

# Install Windows IoT Core Project Templates
After the installation is complete, install the Windows IoT Core Project Templates from [here](https://visualstudiogallery.msdn.microsoft.com/55b357e1-a533-43ad-82a5-a88ac4b01dec).

# Enable Developer Mode on your Windows 10 Development Device
When you are developing on Windows 10, you choose what tasks you want to enable on the device. This includes any devices - Windows 10 desktops, tablets and phones. You can enable a device for development, or just app side loading. To enable _Developer mode_ on your Windows 10 device:

1. Click the Windows icon (typically in the lower-left of the screen, on the left-most side of the toolbar). 
2. Type __Update__ and select _Windows Update settings_ from the _Best match_ list. This will open the __UPDATE & SECURITY__ settings page. 
3. Click on __For developers__ in the left sidebar.
4. Ensure the __Developer mode__ radio button is selected.
5. Save your changes and close the _Settings_ window.  

# Create a Microsoft Azure Trial Account
In this lab series you will use Microsoft Azure as the cloud backend for your IoT solution. If you don't already have an Azure account, go to [https://azure.microsoft.com/en-us/pricing/free-trial/](https://azure.microsoft.com/en-us/pricing/free-trial/) to start a free trial of Microsoft Azure. You may need a credit card for identity verification, but the trial is completely free. If you have an MSDN Subscription you may be eligible for free credits to Microsoft Azure every month. Check your [MSDN account](https://msdn.microsoft.com/subscriptions/manage/) page for details.

# Conclusion &amp; Next Steps
In this Getting Started section you prepared your development machine for the subsequent labs in the workshop. The next step is to configure your prototyping board to become a connected Thing. 

<a class="radius button small" href="{{ site.url }}/workshop/thingy-4-windows/setup-rpi2/">Go to 'Setting Up Your Raspberry Pi 2' ›</a>