---
layout: page-fullwidth
title: "Arduino Thing Labs"
subheadline: "Building Connected Things with Arduino programmed boards"
teaser: "In these Labs you will begin by learning the basics of working with micro-controllers and sensors, and move on to connecting them to the Internet. You will learn how to leverage Cloud IoT services to collect data and control devices and use advanced services like machine learning and analytics to discover insights using your Things."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [arduino, redboard, maker-101]
permalink: /device/arduino/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

# Getting Started
This is a step-by-step guide to preparing your computer for the Arduino labs.

Estimated time to complete: 20-minutes. [Go to lab ->](/device/arduino/getting-started)

# Thing 101 Labs

## Setting Up Your Arduino
In this lab you will install the Standard Firmatta onto your Arduino. 

Estimated time to complete: 20-minutes. [Go to lab ->](/device/arduino/setup-arduino)

## Writing Digital Output
In this lab you will use one of the digital output pins to send a signal to an LED.

Estimated time to complete: 20-minutes. [Go to lab ->](/device/arduino/writing-digital-output)

## Reading Analog Input
In this lab you will read input from an analog sensor using a voltage divider.

Estimated time to complete: 20-minutes. [Go to lab ->](/device/arduino/reading-analog-input)

## Input Controls Output
In this lab you will use analog input to control digital output.

Estimated time to complete: 20-minutes. [Go to lab ->](/device/arduino/input-controls-output)

# Connected Thing Labs
There are options for the Connected Things Labs when coding with JavaScript depending on the board you are working with and the sensors, devices or shields you have available. Regardless of which option you pursue, you must first setup your Microsoft Azure IoT Hub. Once you have done that, return to this page to select the next lab based on your hardware.

## Setting Up Azure IoT Hub
In this lab you will provision a new Azure IoT Hub. Once you have the IoT Hub created, you will be able to create a new Azure IoT device (a software reference to your physical device) that you will use to send telemetry to Azure.

Estimated time to complete: 20-minutes. [Go to lab ->](/lang/js/setup-azure-iot-hub)

## Most Boards, using LEDs and a Photoresistor
Most maker and IoT starter kits come with at least a couple of basic sensors. For the labs in this section you will need a couple of LEDs, a photoresistor and a couple of static resistors (10k Ohm and either 330 Ohm or 560 Ohm). 

### Sending Telemetry to the Cloud
In this lab you will write a Node.js application that runs on a hub (your development machine) and collects data from a development board and sends it up to your Azure IoT Hub.

Estimated time to complete: 20-minutes. [Go to lab ->](/lang/js/sending-telemetry)

## Arduino or Particle Photon with SparkFun Weather Shield
The SparkFun Weather Shield is a great option for experimenting with IoT. The Weather Shield comes in form-factors for the Arduino Uno and SparkFun RedBoard and for the Particle Photon. It has both a humidity sensor and a barometer and is capable of collecting humidity, temperature, barometric pressure and elevation (based on pressure). The Arduino variant also includes a light sensor. Both the Photon and the Arduino have onboard LEDs making it easy to use this board and shield combination to run several IoT experiments. 

### Sending Telemetry to the Cloud
In this lab you will write a Node.js application that runs on a hub (your development machine) and collects data from a development board and sends it up to your Azure IoT Hub.

Estimated time to complete: 20-minutes. [Go to lab ->](/lang/js/weather/sending-telemetry)

# Cloud IoT Service Labs

## Visualizing IoT Data with Power BI
In this lab you will create visualizations of IoT data using Microsoft Power BI.

Estimated time to complete: 30-minutes. [Go to lab ->](/visualize-iot-with-powerbi)