---
layout: page-fullwidth
title: "Preparing the Arduino"
subheadline: "Microsoft Azure IoT Connected Weather Station Project"
teaser: "In this lab you will prepare your Ardunio Uno or SparkFun RedBoard for use in this lab series."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [javascript, node.js, johnny-five, arduino, redboard]
author: doug_seven
permalink: /lang/js/weather-station/setup-arduino/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

For this lab series you are using an Arduino Uno or SparkFun RedBoard prrgrammed with Arduino. These are excellent prototyping boards for _Things_, but neither of them has Wi-Fi capabilities built in. If you pursue prototyping a real _Thing_ you can still use these boards, and add Wi-Fi capabilities to them through additional components (e.g. Wi-Fi shields). This lab series is designed to teach you how to build an IoT solution using the _gsateway_ patterm, similar to how consumer products like SmartThings, Lowes Iris, or Phillips Hue works. For the first few labs you will learn how to create Node.js applications that run on your PC and controls the baord through a USB connection. If you choose to continue with these labs on your own, you will learn how to deploy the Node.js applications to a gateway device capable of running the Node.js application, such as a Raspberry Pi or an Arduino Y&uacute;n.

# Bill of Materials
You can use one of the following SparkFun Wish Lists (choose only one):

<script src="https://www.sparkfun.com/wish_lists/120920.js"></script>

__OR__  

<script src="https://www.sparkfun.com/wish_lists/121094.js"></script>

<blockquote>
	SOLDERING REQUIRED: The SparkFun Weather Shield for Arduino does not come with the stackable headers soldered on. You must solder them on yourself.
</blockquote>

# Install the Arduino IDE
While you won't use the Arduino IDE very much in the set of labs, it is necessary for a couple of things. For one thing, installing the Arduino IDE also installs the USB drivers for the Arduino board.

Go to [http://www.arduino.cc](http://www.arduino.cc) and follow the links to download the latest version of the Arduino IDE. Make sure that the checkbox for the USB driver is selected during install (it typically is by default).

# Upload the Standard Firmata to your Arduino
For this lab series you will use tools that require your Arduino to have the Standard Firmatta loaded. Fortunately, this is easy to do using the Arduino IDE.

1. Connect your Arduino board via USB to your computer.
2. Open the Arduino IDE.
3. Using the _Tools_ menu, select _Board_ and make sure the type of board you are using (Arduino Uno or Arduino Y&uacute;n) is selected.
4. Using the _Tools_ menu, select _Port_ and make sure your board is selected on a COM port.
5. Using the _File_ menu, select _Examples_ > _Firmata_ > _StandardFirmata_. This will open a new Arduino IDE window with the code for the Standard Firmata loaded.
6. Using the _Sketch_ menu, select _Upload_. This will upload the firmware to your Arduino (you should see the lights on the Arduino blink while this happens).
7. Close all Arduino IDE windows.

# Connecting the Weather Shield
The weather shield comes equipped with two onboard sensors - a HTU21D humidity sensor capable of gathering both humidity and temperature, and a MPL3115A2 barometric pressure sensor. There are connections for other optional sensors, such as wind, rain and soil moisture sensors (we will not be working with the optional sensors in this lab series). Simply plug the Photon into the weather shield so that the USB port on the Photon is on the same side as the RJ-11 plugs on the weather shield. 

<img src="{{ site.url }}/images/devices/arduino-weather-station.jpg"/>

# Conclusion &amp; Next Steps
Now that your board is configured and ready you can write the app that will collect data and send it to the Cloud.

<a class="radius button small" href="{{ site.url }}/lang/js/weather-station/sending-telemetry/">Go to 'Sending Telemetry to the Cloud' ›</a>

[getting-started]: ../getting-started/
[setup-azure-iot-hub]: ../setup-azure-iot-hub/
[setup-photon]: ../setup-photon/
[setup-arduino]: ../setup-arduino/
[sending-telemetry]: ../sending-telemetry/
[visualize-iot-with-powerbi]: ../visualize-iot-with-powerbi/