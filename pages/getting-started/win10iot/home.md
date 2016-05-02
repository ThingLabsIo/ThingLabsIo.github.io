---
layout: "page-fullwidth"
title: "Getting Started with Windows 10 IoT"
subheadline: "Building Connected Things with Windows 10 IoT and Microsoft Azure"
teaser: "This is a step-by-step guide to preparing your computer for labs that use Windows 10 IoT."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [raspberry-pi, windows-10, c#, iot, maker]
permalink: /getting-started/win10iot/
---
# Table of Contents
*  Auto generated table of contents
{:toc}

Many of the labs, tutorials, and workshops on ThingLabs.io use Windows 10 IoT, typically running on a Raspberry Pi 2 or 3, although other boards are also capable of running Windows 10 IoT. If you will be going through any of these labs, tutorials, or workshops you need to first prepare your development environment. This lab is meant to be done before any other labs, tutorials, or workshops to ensure your development environment is ready.

# Requirements
The labs, tutorials, and workshops that use Windows 10 IoT depend on the following:

* Windows 10 (10.0.10240 or higher)
* Visual Studio 2015 Community Edition or better.
* Visual Studio Tools for the Windows 10 Universal Windows Platform.
* Windows IoT Core Project Templates.
* The development machine musht have _Developer Mode_ enabled.
* A Microsoft Azure account.

# Install Windows 10
Make sure you are running the public release of Windows 10 (version 10.0.10240 or higher). You can upgrade from [here](http://www.microsoft.com/en-us/software-download/windows10){:target="_blank"}. If you are already running Windows 10, you can find your current build number by clicking the _Start_ button (the Windows icon, typically in the lower-left of the screen, on the left-most side of the toolbar), typing `winver`, and pressing enter.

# Enable Developer Mode on your Windows 10 Development Device
When you are developing on Windows 10, you choose what tasks you want to enable on the device. This includes any devices - Windows 10 desktops, tablets, or phones. You can enable a device for development, or just app side loading. For the labs, tutorials, and workshops on ThingLabs.io you will need to enable _Developer mode_ on your Windows 10 device:

1. Click the _Start_ button. 
2. Type __Update__ and select _Windows Update settings_ from the _Best match_ list. This will open the __UPDATE & SECURITY__ settings page. 
   
   ![Search for Update Settings]({{ site.urlimg }}/getting-started/windows-update-settings.png)
   
3. Click on __For developers__ in the left sidebar.
4. Ensure the __Developer mode__ radio button is selected. If it was not selected, select it and click __Yes__ to switch your Windows 10 installation to developer mode.

   ![Developer Modes]({{ site.urlimg }}/getting-started/developer-mode.png)
   
5. Save your changes and close the _Settings_ window.  

# Install a Visual Studio 2015
Install Visual Studio 2015 from [VisualStudio.com](https://www.visualstudio.com/){:target="_blank"}. You can use the free _Community edition_, or any other higher edition. When you are installing Visual Studio, do the following:

1. Select __Custom__ install.
2. Ensure __Universal Windows App Development Tools -> Tools and Windows SDK__ is checked before completing the installation.

![Install UWP]({{ site.urlimg }}/getting-started/install_uwp.png)

## Update Visual Studio 2015
If you already have Visual Studio 2015 installed, install _Update 1_ from the _Extensions and Updates_ dialog in Visual Studio or from [here](http://go.microsoft.com/fwlink/?LinkID=691134){:target="_blank"}.

> If you already have the Univeral Windows App Development tools (UWP Tools), they will be updated. If you don't have the UWP Tools, you can select to add them to Visual Studio during the upgrade.

## Verify Visual Studio
Verify the Visual Studio installation version. From Visual Studio...

1. Select __Help__ > __About Microsoft Visual Studio__ 
2. Verify the version is __14.0.24720.00 Update 1__ or higher. 

# Install Windows IoT Core Project Templates
Install the Windows IoT Core Project Templates from [here](https://visualstudiogallery.msdn.microsoft.com/55b357e1-a533-43ad-82a5-a88ac4b01dec){:target="_blank"}. 

Alternatively, the templates can be found by searching for Windows IoT Core Project Templates in the Visual Studio Gallery or directly from Visual Studio in the _Extension and Updates_ dialog (_Tools_ > _Extensions and Updates_ > _Online_).

# Create a Microsoft Azure Trial Account
In this lab series you will use Microsoft Azure as the cloud backend for your IoT solution. If you don't already have an Azure account, go to [https://azure.microsoft.com/en-us/pricing/free-trial/](https://azure.microsoft.com/en-us/pricing/free-trial/){:target="_blank"} to start a free trial of Microsoft Azure. You may need a credit card for identity verification, but the trial is completely free. If you have an MSDN Subscription you may be eligible for free credits to Microsoft Azure every month. Check your [MSDN account](https://msdn.microsoft.com/subscriptions/manage/){:target="_blank"} page for details.

# Conclusion &amp; Next Steps
In this lab, you prepared your development environment for labs, tutorials, and workshops on __ThingLabs.io__ that use Windows 10 IoT. 

The next step is to configure your prototypong board and begin building a _Thing_. Currently the only Windows 10 prototyping board used in labs, tutorials, and workshops on ThingLabs is the Raspberry Pi (2 and 3).

## Raspberry Pi 2

<a class="radius button small" href="{{ site.url }}/getting-started/win10iot/rpi2/">Go to 'Setting Up Your Raspberry Pi 2' ›</a>

## Raspberry Pi 3 (or a custom installation on Raspberry Pi 2)

<a class="radius button small" href="{{ site.url }}/getting-started/win10iot/rpi-custom/">Go to 'Setting Up Your Raspberry Pi with a Custom Image' ›</a>