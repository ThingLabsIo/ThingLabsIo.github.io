---
layout: page-fullwidth
title: "Setting Up the Arduino"
subheadline: "Microsoft Azure IoT Lab 2"
teaser: "In this lab you will prepare your Ardunio Uno or SparkFun RedBoard for use in this lab series."
show_meta: true
comments: true
header: no
breadcrumb: true
categories:
    - arduino
    - iot-labs
    - maker-101
author: doug_seven
permalink: "/setup-arduino/"
---

### Table of Contents
*  Auto generated table of contents
{:toc}

For this lab series you are using an Arduino Uno or SparkFun RedBoard prrgrammed with Arduino. These are excellent prototyping boards for _Things_, but neither of them has Wi-Fi capabilities built in. If you pursue prototyping a real _Thing_ you can still use these boards, and add Wi-Fi capabilities to them through additional components (e.g. Wi-Fi shields). This lab series is designed to teach you how to build an IoT solution using the _gsateway_ patterm, similar to how consumer products like SmartThings, Lowes Iris, or Phillips Hue works. For the first few labs you will learn how to create Node.js applications that run on your PC and controls the baord through a USB connection. If you choose to continue with these labs on your own, you will learn how to deploy the Node.js applications to a gateway device capable of running the Node.js application, such as a Raspberry Pi or an Arduino Y&uacute;n.

## Bill of Materials
The following hardware is needed to complete these labs with the Arduino:

1. [SparkFun RedBoard Programmed with Arduino - $19.95](https://www.sparkfun.com/products/12757) or [Arduino Uno R3 - $24.95](https://www.sparkfun.com/products/11021)
2. USB cable to connect the Arduino to your computer (the type will vary depending on the Arduino board you are using)

## Install the Arduino IDE
While you won't use the Arduino IDE very much in the set of labs, it is necessary for a couple of things. For one thing, installing the Arduino IDE also installs the USB drivers for the Arduino board.

Go to [http://www.arduino.cc](http://www.arduino.cc) and follow the links to download the latest version of the Arduino IDE. Make sure that the checkbox for the USB driver is selected during install (it typically is by default).

## Upload the Standard Firmata to your Arduino
For this lab series you will use tools that require your Arduino to have the Standard Firmatta loaded. Fortunately, this is easy to do using the Arduino IDE.

1. Connect your Arduino board via USB to your computer.
2. Open the Arduino IDE.
3. Using the _Tools_ menu, select _Board_ and make sure the type of board you are using (Arduino Uno or Arduino Y&uacute;n) is selected.
4. Using the _Tools_ menu, select _Port_ and make sure your board is selected on a COM port.
5. Using the _File_ menu, select _Examples_ > _Firmata_ > _StandardFirmata_. This will open a new Arduino IDE window with the code for the Standard Firmata loaded.
6. Using the _Sketch_ menu, select _Upload_. This will upload the firmware to your Arduino (you should see the lights on the Arduino blink while this happens).
7. Close all Arduino IDE windows.

## Conclusion &amp; Next Steps
That's it for now. You are ready to start the next lab: [Sending Telemetry to the Cloud =>][sending-telemetry].

{% include next-previous-post-in-category.html %}

[getting-started]: /getting-started/
[setup-azure-iot-hub]: /setup-azure-iot-hub/
[setup-photon]: /setup-photon/
[setup-arduino]: /setup-arduino/
[sending-telemetry]: /sending-telemetry/
[visualize-iot-with-powerbi]: /visualize-iot-with-powerbi/