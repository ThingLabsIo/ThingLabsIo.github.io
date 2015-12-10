---
layout: page-js
title: "JavaScript, Node.js & JohnnyFive Labs"
subheadline: "Building Connected Things with JavaScript"
teaser: "In these Labs you will begin by learning the basics of working with micro-controllers and sensors, and move on to connecting them to the Internet. You will learn how to leverage Cloud IoT services to collect data and control devices and use advanced services like machine learning and analytics to discover insights using your Things."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [photon, maker-101]
permalink: /lang/js/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

# The IoT Gateway Pattern
The labs in this series build on each other to enable you to prototype your own Internet of Things (IoT) devices. In this lab you will build a solution using a 'Gateway' pattern, where a device (the Gateway) collects data from one or more connected devices/sensors and aggregates the data before sending it to the cloud. For the gateway, you will use Node.js and an open source framework for interacting with hardware, called Johnny-Five, which works as a baseline control kit for hardware projects, including Arduino, Particle Photon, Raspberry Pi, Beagle Bone, and Intel Edison boards. This enables you to write applications in JavaScript that can run as a gateway either on your computer or on a hub device (like a Raspberry Pi 2) connected to an variety of devices.

<img src="/images/gatewaypattern.png"/>

# Getting Started
This is a step-by-step guide to preparing your computer for the IoT labs using JavaScript, Node.js and Johnny-Five.

<a class="radius button small" href="{{ site.url }}/lang/js/getting-started/">Estimated time to complete: 20-minutes. Go to  'Getting Started' ›</a>

# Thing 101 Labs

## Setting Up Your Device
In this lab you will install firmware onto your board that will enable communication with the gateway application. How you configure the board and what firmware you install will depend on the board you choose to use.

### Arduino Uno, Y&uacute;n / SparkFun RedBoard
In this lab you will install the Standard Firmatta onto your Arduino. 

<a class="radius button small" href="{{ site.url }}/lang/js/setup-arduino/">Estimated time to complete: 20-minutes. Go to  'Setting Up Your Arduino' ›</a>

### Particle Photon
In this lab you will install the VoodooSpark firmware on your Photon. 

<a class="radius button small" href="{{ site.url }}/lang/js/setup-photon/">Estimated time to complete: 20-minutes. Go to  'Setting Up Your Particle Photon' ›</a>

## Writing Digital Output
In this lab you will use one of the digital output pins to send a signal to an LED.

<a class="radius button small" href="{{ site.url }}/lang/js/writing-digital-output/">Estimated time to complete: 20-minutes. Go to  'Writing Digital Output' ›</a>

## Reading Analog Input
In this lab you will read input from an analog sensor using a voltage divider.

<a class="radius button small" href="{{ site.url }}/lang/js/reading-analog-input/">Estimated time to complete: 20-minutes. Go to  'Reading Analog Input' ›</a>

## Input Controls Output
In this lab you will use analog input to control digital output.

<a class="radius button small" href="{{ site.url }}/lang/js/input-controls-output/">Estimated time to complete: 20-minutes. Go to  'Input Controls Output' ›</a>

# Connected Thing Labs
There are options for the Connected Things Labs when coding with JavaScript depending on the board you are working with and the sensors, devices or shields you have available. Regardless of which option you pursue, you must first setup your Microsoft Azure IoT Hub. Once you have done that, return to this page to select the next lab based on your hardware.

## Setting Up Azure IoT Hub
In this lab you will provision a new Azure IoT Hub. Once you have the IoT Hub created, you will be able to create a new Azure IoT device (a software reference to your physical device) that you will use to send telemetry to Azure.

<a class="radius button small" href="{{ site.url }}/lang/js/setup-azure-iot-hub/">Estimated time to complete: 20-minutes. Go to  'Setting Up Azure IoT Hub' ›</a>

## Sending Telemetry to the Cloud
In this lab you will write a Node.js application that runs on a hub (your development machine) and collects data from a development board and sends it up to your Azure IoT Hub.

<a class="radius button small" href="{{ site.url }}/lang/js/sending-telemetry/">Estimated time to complete: 20-minutes. Go to  'Sending Telemetry to the Cloud' ›</a>

# Cloud IoT Service Labs
In these labs you will learn how to use Cloud services to gain insights and derive value from the data you are collecting from your _Thing_.

## Visualizing IoT Data with Power BI
In this lab you will create visualizations of IoT data using Microsoft Power BI.

<a class="radius button small" href="{{ site.url }}/lang/js/visualize-iot-with-powerbi/">Estimated time to complete: 20-minutes. Go to  'Visualizing IoT Data with Power BI' ›</a>