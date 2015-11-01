---
layout: page-fullwidth
title: "Prepare for Deployment"
subheadline: "IoT Lab 4a"
teaser: "Prepping the Yun for Johnny Five and Nitrogen."
show_meta: true
comments: true
header: no
breadcrumb: true
categories:
    - iot-arduino-labs
    - maker-101
author: "Doug Seven"
permalink: "/arduino/04a/"
---

### Table of Contents
*  Auto generated table of contents
{:toc}

# Prepping the Yun for Johnny-Five and Nitrogen
In this lesson you will prepare the Arduino Yun for deployment of the Johnny Five Node.js application onto the device, which enables the device to run autonomously disconnected from a computer.

## Open the Instruction to Configure Wi-Fi on the Yun  
You don't need to complete the steps yet, but you will save yourself a headache by opening [these](http://arduino.cc/en/Guide/ArduinoYun#toc13) instructions up and having them ready.  

## Upgrade to Latest Version of OpenWRT  
When you first power on the Yún, it will create a WiFi network called ArduinoYun-XXXXXXXXXXXX. Connect your computer to this network.  
  
Follow the instructions [here](http://arduino.cc/en/Tutorial/YunSysupgrade) to upgrade to the latest version of OpenWRT (the Linux distribution running on the Atheros processor).  

## Now Configure Wi-Fi on the Yun  
The following steps require the Arduino to have internet access. Follow the instructions [here](http://arduino.cc/en/Guide/ArduinoYun#toc13) to get the Arduino connected to your Wi-Fi.

## Expand Onto the microSD Card  
Follow the instructions [here](http://arduino.cc/en/Tutorial/ExpandingYunDiskSpace) to expand the Arduino disk space onto the microSD card (we need the space because the Arduino has only a small amount of flash storage).  
>When prompted for the expansion size enter **4096**  

## Add a Swap File  
Using the Serial Monitor or SSH (e.g. PuTTY, Terminal, etc.), enter the following:  

    dd if=/dev/zero of=/swapfile count=512 bs=1M  
    mkswap /swapfile
    swapon /swapfile

Using SSH (e.g. PuTTY, Terminal, etc.), edit fstab with vi  

    vi /etc/config/fstab  

Append the following to the file and use the :wq vi command to save and exit the file  

    config swap  
                option device    /swapfile  

Enable the swap file  

    /etc/init.d/fstab enable

[Swap Reference](http://www.cambus.net/getting-started-with-openwrt/)
## Install the BINUTILS Package

    opkg update
    opkg install binutils

## Get YUN-GCC and Force Install It to root  

    wget  http://downloads.arduino.cc/openwrtyun/1/packages/yun-gcc_4.6.2-2_ar71xx.ipk
    
The following step can take from 5 to 20 minutes.  

    opkg install --force-overwrite yun-gcc_4.6.2-2_ar71xx.ipk
    opkg update

>vi sometimes generates xterm errors; if it does, do the following:  

    opkg install vim

## Install Node  

    opkg install node

[Node.js Reference](http://blog.arduino.cc/2014/05/06/time-to-expand-your-yun-disk-space-and-install-node-js/)
## Serialport/Johnny-Five/Firmata Update  

    opkg install node-serialport

The following step can take several minutes.  

    npm install –g johnny-five

## Disable the Bridge Script  
Remove the bridge setup from the inittab script by commenting out the **ttyATH0** line using the stream editor. This will replace the first argument (ttyAHT0) with the second argument (# ttyAHT0) which results in the line being commented out.  

    sed -i -e 's/ttyATH0/# ttyATH0/g' /etc/inittab
  
## Install Nitrogen.js  

    npm install –g nitrogen
    npm install –g nitrogen-cli

## Install the AllJoyn Samples  

    opkg install alljoyn-samples alljoyn-sample_apps
    
Congratulations! Your Arduino Y&uacute;n is configured and ready for you to [deploy Nitrogen apps to it](/arduino/0B/).