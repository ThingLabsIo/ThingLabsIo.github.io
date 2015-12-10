---
layout: page-fullwidth
title: "ThingLabs by Devices"
subheadline: "By Device"
teaser: "Select your lab path by the devie you have - Arduino, RedBoard, Photon, or Raspberry Pi 2 with Windiws 10 IoT Core."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [arduino, photon, redboard, rpi2, iot-labs, maker-101]
author: doug_seven
permalink: /device/
---

### Table of Contents
*  Auto generated table of contents
{:toc}

## Arduino
Arduino is an open-source prototyping platform based on easy-to-use hardware and software. Arduino boards are able to read inputs - light on a sensor, a finger on a button, or a Twitter message - and turn it into an output - activating a motor, turning on an LED, publishing something online. You can tell your board what to do by sending a set of instructions to the microcontroller on the board. 

As an open-source solution, several vendors have produces Arduino and Arduino-compatible boards, including Smart Projects, AdaFruit and SparkFun. Some of the most commonly used Arduino boards are referenced here and can be used for the labs on ThingLabs. In general we treat them all the same (as Arduino boards), with exceptions only when there is a unique characteristic, such as the Wi-Fi and Linux capabilities of the Arduino Y&uacute;n.

### Arduino Uno
The Uno is a microcontroller board based on the ATmega328P. It has 14 digital input/output pins (of which 6 can be used as PWM outputs), 6 analog inputs, a 16 MHz quartz crystal, a USB connection, a power jack, an ICSP header and a reset button. It contains everything needed to support the microcontroller.

For technical specs, see [the official Arduino specs](https://www.arduino.cc/en/Main/ArduinoBoardUno).

[Go to Adruino Labs =>][arduino]

### Arduino Y&uacute;n
The Arduino Yún is a microcontroller board based on the ATmega32u4 and the Atheros AR9331. The Atheros processor supports a Linux distribution based on OpenWrt named OpenWrt-Yun. The board has built-in Ethernet and WiFi support, a USB-A port, micro-SD card slot, 20 digital input/output pins (of which 7 can be used as PWM outputs and 12 as analog inputs), a 16 MHz crystal oscillator, a micro USB connection, an ICSP header, and a 3 reset buttons.

For technical specs, see [the official Arduino specs](https://www.arduino.cc/en/Main/ArduinoBoardYun).

[Go to Adruino Labs =>][arduino]

### SparkFun RedBoard
At SparkFun, they use many Arduinos and they're always looking for the simplest, most stable one. Each board is a bit different and no one board has everything they want, so they decided to make their own version that combines all their favorite features. The SparkFun RedBoard combines the simplicity of the Arduino Uno’s Optiboot bootloader (which is used in the Pro series), the stability of the FTDI, and the R3 shield compatibility of the latest Arduino Uno R3. The RedBoard has all of the hardware peripherals you need: 14 Digital I/O pins with 6 PWM pins, 6 Analog Inputs, UART, SPI and external interrupts. SparkFun also broke out the SDA, SCL and IOREF pins that showed up on the UNO R3, so the RedBoard will be compatible with future shields. This version adds an SMD ISP header for use with shields.

For technical specs, see [the official SparkFun specs](https://www.sparkfun.com/products/12757)

[Go to Adruino Labs =>][arduino]

## Particle Photon
Particle is a prototype-to-production platform for developing an Internet of Things product. The Photon is a $19 development kit for creating Wi-Fi connected products. It's based on Broadcom's WICED architecture, and combines a powerful STM32 ARM Cortex M3 microcontroller and a Broadcom Wi-Fi chip (the same one that's in Nest Protect, LIFX, and Amazon Dash). 

For technical specs, see [the official Particle Photon specs](https://www.particle.io/prototype#photon)

[Go to Particle Photon Labs =>][photon]

## RaspBerry Pi
The Raspberry Pi (aka RPi) is a series of credit card–sized single-board computers developed in the United Kingdom by the _Raspberry Pi Foundation_ with the intention of promoting the teaching of basic computer science in schools and developing countries. 

### Raspberry Pi 2
The RPi2 is the second generation RPi. It replaced the original RPi1 in February 2015. It has a 900MHz quad-core ARM Cortex-A7 CPU, 1GB RAM, 4 USB ports, 40 GPIO pins, full HDMI port, Ethernet port, combined 3.5mm audio jack and composite video, camera interface (CSI), display interface (DSI), micro SD card slot, and VideoCore IV 3D graphics core.

Because it has an ARMv7 processor, it can run the full range of ARM GNU/Linux distributions, including Snappy Ubuntu Core, as well as Microsoft Windows 10 IoT Core. The RPi2 has an identical form factor to the previous (RPi1) and has complete compatibility with RPi1.

For technical specs, see [the official Raspberry Pi 2 specs](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/)

#### Windows 10 IoT Core
You can build IoT projects with the RPi2 running Windows 10 IoT Core and using the .NET Framework and C#. 

[Go to RPi2 Windows IoT Labs =>][rpi2]

#### Raspian
Coming soon!

### Raspberry Pi Zero
The Raspberry Pi Zero is a small, five-dollar computer (smaller than the RPi2). A tiny Raspberry Pi that’s affordable enough for any project! The RPiZero has a 1Ghz Single-core CPU, 512MB RAM, mini HDMI and USB On-The-Go ports, micro USB power, HAT-compatible 40-pin header, composite video and reset headers.

For technical specs, see [the official Raspberry Pi Zero specs](https://www.raspberrypi.org/products/pi-zero/)

Coming soon!

## SparkFun Thing (ESP8266)
The SparkFun ESP8266 Thing is a breakout and development board for the ESP8266 WiFi SoC – a leading platform for Internet of Things (IoT) or WiFi-related projects. The SparkFun ESP8266 Thing is a relatively simple board. The pins are broken out to two parallel, breadboard-compatible rows. USB and LiPo connectors at the top of the board provide power – controlled by the nearby ON/OFF switch. LEDs towards the inside of the board indicate power, charge, and status of the IC. The ESP8266’s maximum voltage is 3.6V, so the Thing has an onboard 3.3V regulator to deliver a safe, consistent voltage to the IC. That means the ESP8266’s I/O pins also run at 3.3V, you’ll need to level shift any 5V signals running into the IC. A 3.3V FTDI Basic is required to program the SparkFun ESP8266 Thing, but other serial converters with 3.3V I/O levels should work just fine as well. The converter does need a DTR line in addition to the RX and TX pins.

For technical specs, see [the official SparkFun Thing specs](https://www.sparkfun.com/products/13231)

Coming soon!

## Intel Edison
Designed for expert makers, entrepreneurs, and some industrial IoT applications, the Intel® Edison compute module provides ease-of-development for a range of prototyping projects or commercial ventures when performance matters. High performance, dual-core CPU and single core micro-controller supports complex data collection in a low power package, integrated Wi-Fi certified in 68 countries, Bluetooth® 4.0 support, 1GB DDR and 4GB flash memory simplifies configuration and increases scalability, 40 multiplexed GPIO interfaces with expansion board options for total project design. flexibility

For technical specs, see [the official Intel Edison specs](http://www.intel.com/content/www/us/en/do-it-yourself/edison.html)

Coming soon!

[arduino]: /device/arduino
[photon]: /device/photon
[rpi2]: /device/rpi2