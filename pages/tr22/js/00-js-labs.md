---
layout: page-tr22
title: "JavaScript, Node.js & JohnnyFive Labs"
subheadline: "Building Connected Things with Node.js"
teaser: "In these Labs you will begin by learning the basics of working with micro-controllers and sensors, and move on to connecting them to the Internet. You will learn how to leverage Cloud IoT services to collect data and control devices and use advanced services like machine learning and analytics to discover insights using your Things."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [photon, maker-101]
permalink: /tr22/js/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

# The IoT Gateway Pattern
The labs in this series build on each other to enable you to prototype your own Internet of Things (IoT) devices. In this lab you will build a solution using a 'Gateway' pattern, where a device (the Gateway) collects data from one or more connected devices/sensors and aggregates the data before sending it to the cloud. For the gateway, you will use Node.js and an open source framework for interacting with hardware, called Johnny-Five, which works as a baseline control kit for hardware projects, including Arduino, Particle Photon, Raspberry Pi, Beagle Bone, and Intel Edison boards. This enables you to write applications in JavaScript that can run as a gateway either on your computer or on a hub device (like a Raspberry Pi 2) connected to an variety of devices.

<img src="/images/gatewaypattern.png"/>

# Thing 101 Labs

## Hello, World
In this lab you will use one of the digital output pins to send a signal to an LED.

<a class="radius button small" href="{{ site.url }}/tr22/js/writing-digital-output/">Estimated time to complete: 20-minutes. Go to  'Writing Digital Output' ›</a>

# Connected Thing Labs
There are options for the Connected Things Labs when coding with JavaScript depending on the board you are working with and the sensors, devices or shields you have available. Regardless of which option you pursue, you must first setup your Microsoft Azure IoT Hub. Once you have done that, return to this page to select the next lab based on your hardware.

## Setting Up Azure IoT Hub
In this lab you will provision a new Azure IoT Hub. Once you have the IoT Hub created, you will be able to create a new Azure IoT device (a software reference to your physical device) that you will use to send telemetry to Azure.

<a class="radius button small" href="{{ site.url }}/tr22/js/setup-azure-iot-hub/">Estimated time to complete: 20-minutes. Go to  'Setting Up Azure IoT Hub' ›</a>

## Sending Telemetry to the Cloud
In this lab you will write a Node.js application that runs on a hub (your development machine) and collects data from a development board and sends it up to your Azure IoT Hub.

<a class="radius button small" href="{{ site.url }}/tr22/js/sending-telemetry/">Estimated time to complete: 20-minutes. Go to  'Sending Telemetry to the Cloud' ›</a>

# Cloud IoT Service Labs
In these labs you will learn how to use Cloud services to gain insights and derive value from the data you are collecting from your _Thing_.

## Visualizing IoT Data with Power BI
In this lab you will create visualizations of IoT data using Microsoft Power BI.

<a class="radius button small" href="{{ site.url }}/tr22/js/visualize-iot-with-powerbi/">Estimated time to complete: 20-minutes. Go to  'Visualizing IoT Data with Power BI' ›</a>