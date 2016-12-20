---
layout: "page-fullwidth"
title: "ESP8266 Workshop: Getting Started"
subheadline: "Building Connected Things with Windows 10 IoT and Microsoft Azure"
teaser: "This is a step-by-step guide to preparing your computer for the ESP8266 Workshop."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [esp8266, lua, MQTT, iot, maker]
permalink: /workshop/esp8266/getting-started/
---
# Table of Contents
*  Auto generated table of contents
{:toc}

# Preparing for the ESP8266 Workshop
The labs in the ESP8266 Workshop build on each other to enable you to prototype your own Internet of Things (IoT) devices. 
This Getting Started section sets up your computer so that you'll be able to build software that runs on the ESP8266. This Getting Started section also prepares the ESP8266 to accept the software that you've created.

# Bill of Materials
In the ESP8266 Workshop you will need the following:

1. [Microsoft Azure IoT Starter Kit w/ Adafruit Feather HUZZAH](https://www.adafruit.com/product/3032)


# Install ESPlorer
Please install [ESPlorer](http://esp8266.ru/esplorer/), source is available from github: [ESPLorer Source](https://github.com/4refr0nt/ESPlorer).
This is the primary tool you will use to program the ESP8266, but first we have to do some preparation. Make sure to select 115200 as your baud rate.

<a class="radius button small" href="http://esp8266.ru/esplorer-latest/?f=ESPlorer.zip">Download Esplorer›</a>

<img src="/images/esplorer.png"/>

# Download and Install Python, esptool.py

1. Install [Python 2.7](https://www.python.org/downloads/release/python-2711/) for your platform.
<a class="radius button small" href="https://www.python.org/ftp/python/2.7.11/python-2.7.11.amd64.msi">Python 2.7 for Windows</a>
<a class="radius button small" href="https://www.python.org/ftp/python/2.7.11/python-2.7.11-macosx10.6.pkg">Python 2.7 for Mac</a>
2. Once done, install [esptool](https://github.com/themadinventor/esptool/blob/master/README.md) by doing the following: ```pip install esptool```
To run the pip install on Windows, you might have to either add the Python Tools folder to your path (on my machine it's c:\Python27\Tools) or explicitly type it out. 

# Assemble the Parts
Insert the Huzzah Feather carefully into the prototyping board, attach the A to Micro B USB cable to the board and your computer.

<img src="/images/huzzah-feather-board.png" alt="Assembled Parts" style="width: 400px;"/>

# Download and Burn the firmware to your ESP8266

1. [Install Serial Drivers](https://www.silabs.com/products/mcu/Pages/USBtoUARTBridgeVCPDrivers.aspx) for the Huzzah Feather.
<a class="radius button small" href="https://www.silabs.com/Support%20Documents/Software/CP210x_Windows_Drivers.zip">Windows</a>
<a class="radius button small" href="https://www.silabs.com/Support%20Documents/Software/Mac_OSX_VCP_Driver.zip">Mac</a>
<a class="radius button small" href="https://www.silabs.com/Support%20Documents/Software/Linux_3.x.x_VCP_Driver_Source.zip">Linux</a>
2. Plug in the ESP8266 into an open USB port using the cable
3. Identify the USB Port, for me (on OS X) it's: /dev/tty.SLAB_USBtoUART. On Windows, open the "Device Manager" and look under Ports. 
4. Download the [firmware for the ESP8266](https://cdn.rawgit.com/ThingLabsIo/ThingLabsIo.github.io/master/pages/workshop/esp8266/nodemcu_float_dev_pwm_cert.bin)
<a class="radius button small" href="https://cdn.rawgit.com/ThingLabsIo/ThingLabsIo.github.io/master/pages/workshop/esp8266/nodemcu_float_dev_pwm_cert.bin">ESP8266 ThingLabs Firmware</a> and download the [device certificate](https://cdn.rawgit.com/ThingLabsIo/ThingLabsIo.github.io/master/pages/workshop/esp8266/esp_device_cert.bin)
<a class="radius button small" href="https://cdn.rawgit.com/ThingLabsIo/ThingLabsIo.github.io/master/pages/workshop/esp8266/esp_device_cert.bin">ESP8266 Device Cert</a>
5. Burn the firmware and the device cert first, using	

On OSX:
```esptool.py -p /dev/tty.SLAB_USBtoUART write_flash 0x00000 nodemcu_float_dev_pwm_cert.bin 0x84000 esp_device_cert.bin```

On Windows:
```esptool.py -p Com3 write_flash 0x00000 nodemcu_float_dev_pwm_cert.bin 0x84000 esp_device_cert.bin```

This is what you should see if it's successfully putting your firmware on the ESP8266:

<img src="/images/ESP8266-Burn-bin-Output.png" alt="Output while successfully burning firmware." style="width: 400px;"/>

Afterwards, hit the reset button to load up the new firmware.

# Create a Microsoft Azure Trial Account
In the ESP you will use Microsoft Azure as the cloud backend for your IoT solution. If you don't already have an Azure account, go to [https://azure.microsoft.com/en-us/pricing/free-trial/](https://azure.microsoft.com/en-us/pricing/free-trial/) to start a free trial of Microsoft Azure. You may need a credit card for identity verification, but the trial is completely free. If you have an MSDN Subscription you may be eligible for free credits to Microsoft Azure every month. Check your [MSDN account](https://msdn.microsoft.com/subscriptions/manage/) page for details.

# Conclusion &amp; Next Steps
In this lab you prepared your development machine for the following labs. Next, you will learn about how the ESP8266 uses the Lua programming language to create a tiny application development environment.

<a class="radius button small" href="{{ site.url }}/workshop/esp8266/hello-lua-iot/">Go to 'Lab 01: Hello, Lua IoT!' ›</a>
