---
layout: "page-fullwidth"
title: "Configure Your Raspberry Pi"
subheadline: "Building Connected Things with Windows 10 IoT and Microsoft Azure"
teaser: "In this lab, you will configure your Raspberry Pi for use in the labs, tutprials and workshops, including getting it on a network, giving it a unique machine name, and changing the Administrator password."
show_meta: true
comments: true
header: no
breadcrumb: true
categories: [raspberry-pi, windows-10, c#, iot, maker]
permalink: /getting-started/win10iot/rpi-config/
---
# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab, you will install a non-standard version of Windows 10 IoT Core on your Raspberry Pi 2 or 3. The term _non-standard_ refers to images that are not available in the default dropdown list in the Windows 10 IoT Core Dashboard, including older images and Windows Insider images. If you have a Raspberry Pi 3, you will need to use a Windows Insider image. You need to have a valid Microsoft Account (MSA) and need to be signed up for the _Windows Insider Program_ to be able to download the Insider Preview. 

# Get Your Raspberry Pi on a Network
To enable you to remotely deploy and debug applications on the Raspberry Pi you need to connect it to the same network that your development machine is connected to. This can be either a physical network (via an Ethernet cable) or a Wi-Fi network, if you have a wireless adapter from the [list of compatible hardware](https://developer.microsoft.com/en-us/windows/iot/win10/SupportedInterfaces.htm#WiFi-Dongles){:target="_blank"}, or a Raspberry Pi 3, which has built-in wireless capabilities.

## Connect to a Physical Network
Connecting to a physical network is the easiest. Simply plug the Ethernet cable into the Ethernet port on the Raspberry Pi.

## Connect to a Wireless Network
If your board has a Wi-Fi adapter or is a Raspberry Pi 3, and it has not yet be set up, it will start to broadcast itself as a network. Unconfigured boards will begin with "AJ_" (e.g. AJ_58EA6C68).

![Devices broadcasting their ID]({{ site.urlimg }}/getting-started/rpi/dashboard-setup05.png)

1. Go to __My devices__. From my devices, you can discover your device and configure it (including connecting to Wi-Fi).
2. Find your board and click __Configure Device__. If you don’t see your board, make sure that you’ve allowed enough time for your board to boot. If all else fails, reboot your device.
3. Enter your network credentials. Your computer will now connect to your board.

# Set the Machine Name and Password
By default, your board will have the name __minwinpc__ and an Administrator password of __p@ssw0rd__. It is recommended that you change both of these to avoid confusion with other devices on your network.

1. Find your device in the list
2. Click the edit symbol (pencil symbol). This will take you to the _Settings_ page. From the settings page, you can launch Windows Device Portal, and set basic settings.
3. Set the machine name and change the password (highly recommended). All devices start with a default password. The default password is __p@ssw0rd__. It is highly suggested that you change it.
4. Click __Accept__ to accept the changes and reboot the Raspberry Pi

Your Raspberry Pi will reboot and when it is back up and running it will have the new name you gave it, and the Administrator account will use the password you created. 

# Test Device
Quick-Run samples are pre-built and require no compiling or coding to get going. This is a great way to make sure everything is working and easily play with your board.

1. Navigate to __Try some samples__ in the left nav bar.
2. Select _Hello Blinky_
3. Select your board from the drop-down list
4. Launch the sample. 

In the background, the dashboard will temporarily install the sample onto your device. Once loaded, the device will broadcast a web page over the network and IoT Dashboard will automatically connect to it. This lets you control the app without having to plug in a monitor directly to your device.

# Conclusion & Next Steps
You should now be ready to begin any of the [Windows 10 IoT labs]({{ site.url }}/workshop/win10iot/).