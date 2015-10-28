---
layout: "page-fullwidth"
title: "Hello, Windows IoT!"
subheadline: "Raspberry Pi IoT Lab 1"
teaser: "In this lab you will create a simple 'Thing' using Wndows 10 IoT Core and a Raspberry Pi 2."
show_meta: true
comments: true
header: no
breadcrumb: true
categories:
    - iot-rpi2-labs
    - maker-101
author: "Doug Seven"
permalink: /rpi2/01/
---
### Table of Contents
*  Auto generated table of contents
{:toc}

If you haven't already done so, please follow the instructions in [Lab 00: Getting Started](/rpi2/00/) section.

In this lab you will create a simple _Thing_ using the Raspberry Pi and the Unniversal Windows Platform. 

## Bill of Materials
What you will need:

1. [Raspberry Pi 2 - $42.00](http://www.amazon.com/Raspberry-Pi-Model-Project-Board/dp/B00T2U7R7I/)
2. [5V 2A Switching Power Supply w/ 20AWG 6' MicroUSB Cable - $7.95](https://www.adafruit.com/product/1995)
3. [(2) Jumper wires (Male to Female) - $1.95](https://www.adafruit.com/product/1954)
4. [(1) Red LED - 25 for $8.00](http://www.adafruit.com/products/297)
5. [(1) 330 Ohm resistors - 100 for $3.90](http://www.amazon.com/E-Projects-Resistors-Watt-330R-Pieces/dp/B00BVOR6IS/)
6. 8GB micro SD card - class 10 or better. Microsoft suggests [this one](http://www.amazon.com/gp/product/B00IVPU786) or [this one](http://www.amazon.com/SanDisk-Ultra-Micro-SDHC-16GB/dp/9966573445).
7. Micro SD card writer

## Install Windows 10 IoT Core on Raspberry Pi 2
Windows 10 IoT Core is a version of Windows 10 designed to run on small devices, like the Raspberry Pi 2. You can download and install the Windows IoT Core image using the micro SD card. 

1. Download a Windows 10 IoT Core image from the [Microsoft downloads page](http://ms-iot.github.io/content/en-US/Downloads.htm). 
2. Save the ISO to a local folder (e.g. C:\Development\IoTLabs).
3. Double-click the ISO to mount it as a virtual drive. 
4. Install __Windows_10_IoT_Core_RPi2.msi__. This will install the _Windows IoT Core Watcher_ utility and the Raspberry Pi 2 FFU (Field Firmware Update).
5. Eject the virtual drive after the installation is complete.

Now that the utilities and firmware (FFU) are on your development device, you can create a Windows IoT Core image on the SD card.

1. Insert the micro SD card into your SD card writer.
2. Click on the Windows icon (Start menu) and type _WindowsIoT_ - select the __WindowsIoTImageHelper__ from the _Best match_ list.
3. When the tool launches, selct the SD card from the list.
4. Select the FFU file be browsing to __C:\Program Files (x86)\Microsoft IoT\FFU\RaspberryPi2__
5. Once the SD card is complete, safely eject the SD card (use either _Safely Remove Hardware_ from the taskbar, or right-click on the SD card in File Explorer and choose _Eject_ - failing to do so may corupt the SD card.). 

## Connect the Raspberry Pi 2
You are now ready to connect and power on your Raspberry Pi 2.

1. Insert the micro SD card with the Windows 10 IoT Core image on it into your Raspberry Pi 2 (the slot is on the underside, on the opposite edge from the side with the USB ports).
2. Connect a network cable from your local network to the ethernet port on the Raspberry Pi 2. Your development device must be on the same network.
3. Connect an HDMI monitor to the HDMI port on the board.
4. Connect the power supply to the micro USB port on the board. You must power this from the 5V 2A adapter - USB power from your computer is insufficient.

Windows 10 IoT Core will boot on power-up. The first boot may take a few minutes.

<img src="/images/rpi2_defaultapp.png"/>

## Wire Up the RPi2

<img src="/images/rpi2_lab01_bb.png"/>

## Create a Universal App
A Universal Windows app is a Windows experience that is built upon the Universal Windows Platform (UWP), which was first introduced in Windows 8 as the Windows Runtime. The UWP enables you to write an app that targets a device family, such as IoT devices. In fact, the universal app that you write may be able to run on multiple devices families, depending on the device characteristics that it takes advantage of. In this lab you will create a universal app targeting IoT devices running Windows 10. Technically this could be nearly any device, such as a phone, a tablet or a Raspberry Pi 2, however; the universal app you write will access the General Purpose Input/Output (GPIO) of the device, so the app won't actually be compatible with devices that don't have a GPIO.   

### Create a Blank Universal App
Launch Visual Studio and start a new __Blank App (Universal Windows)__ (found in the _C# -> Windows -> Universal_ node).

Name the application _HelloWindowsIoT_.

<img src="/images/rpi2_new_universal.png"/>

Once the solution is created, click on the _Project_ menu and select _Add Reference_.

In the Reference Manager dialog, expand the _Universal Windows_ node and select _Extensions_.

In the list of extensions, check the box next to __Windows IoT Extensions for the UWP__ and click __OK__.

<img src="/images/rpi2_install_iotextensions.png"/>

Open the _MainPage.xaml_ file. This is the layout definition for the inital page that loads when the app is run. Next you will add a few elements to the page.

{% highlight xml %}
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

</Grid>
{% endhighlight %}

## Run the App

<img src="/images/rpi2_lab01_nogpio.png"/>

<img src="/images/rpi2_lab01_arm.png"/>

<img src="/images/rpi2_lab01_remote.png"/>

## Conclusion &amp; Next Steps

Congratulations! You have created a software representation of the physical device that you will build, and the IoT Hub service to connect it to. In the [next lab][nextlab] you will build a Universal Windows Application application that will collect data from the Raspberry Pi and send it to Azure IoT Hub.

[Next Lab ->][nextlab]

{% include next-previous-post-in-category.html %}

[nextlab]: /rpi2/02/