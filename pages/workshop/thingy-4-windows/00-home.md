---
layout: "page-full-day-windows"
title: "ThingLabs Thingy&trade; for Windows IoT Workshop (full day)"
subheadline: "Building Connected Things with Windows 10 and Microsoft Azure"
teaser: "In these IoT labs, you will begin by learning the basics of working with Windows 10 IoT Core connected to sensors and actuators. You will move on to connecting the Thing to the Cloud. You will learn how to leverage Microsoft Azure services to collect data and control devices. Finally, you'll use advanced services like analytics and machine learning to discover insights using your Things."
show_meta: true
comments: false
header: 
    image_fullwidth: workshops/thingy-4-windows/header.jpeg
breadcrumb: true
categories: [raspberry-pi, windows-10, grove, c#, iot, maker]
permalink: /workshop/thingy-4-windows/
---

# Table of Contents
*  Auto generated table of contents
{:toc}


# Workshop Goals
The goals of this workshop are:

+ Educate developers on the IoT stack offered by Microsoft
+ Educate developers on the Universal Windows Platform (UWP)
+ Educate developers on the Azure services related to IoT

By the end of the workshop you will be able to:

+ Build an IoT device using the Raspberry Pi 2 
+ Build a UWP app for Windows IoT Core to run on the Raspberry Pi 2
+ Use Windows IoT Core and the Raspberry Pi 2 to both capture input and present output
+ Create and configure an Azure IoT Hub to manage device-to-cloud and cloud-to-device messaging
+ Connect a UWP application running on the Raspberry Pi 2 to an Azure IoT Hub
+ Build a data pipeline that captures and stores data coming into an IoT Hub
+ Build a visualization of IoT data
+ Build a client application that can send a command to the IoT device via Azure

# Prep Work

## Getting Started
This is a step-by-step guide to preparing your computer for the Thingy for Windows IoT Workshop

<a class="radius button small" href="{{ site.url }}/workshop/thingy-4-windows/getting-started/">Estimated time to complete: 20-minutes. Go to  'Getting Started' ›</a>

## Setting Up Your Raspberry Pi 2
The Raspberry Pi 2 is a low cost credit-card sized computer that plugs into a computer monitor or TV, and uses a standard keyboard and mouse. 

In this section, you will install Windows 10 IoT Core on your Raspberry Pi 2.

<a class="radius button small" href="{{ site.url }}/workshop/thingy-4-windows/setup-rpi2/">Estimated time to complete: 20-minutes. Go to  'Setting Up Your Raspberry Pi 2' ›</a>

## The GrovePi
The GrovePi is a shield stacked on top of the Raspberry Pi 2 without the need for any other connections.  Communication between the GrovePi and the Raspberry Pi 2 occurs over the I2C interface. All Grove modules connect to the universal Grove connectors on the GrovePi shield via the universal 4 pin connector cable. This section provides a description of the GrovePi shield and how it interfaces with the Raspberry Pi 2.

<a class="radius button small" href="{{ site.url }}/workshop/thingy-4-windows/grovepi/">Estimated time to complete: 5-minutes. Go to  'The GrovePi' ›</a>

# Lab Guides

## Lab 01: Hello, Windows IoT!
In this lab, you will build a simple single-circuit device that blinks an LED on and off. (This is the ‘Hello, World! of the IoT space.)

<a class="radius button small" href="{{ site.url }}/workshop/thingy-4-windows/hello-windows-iot/">Estimated time to complete: 20-minutes. Go to  'Lab 01: Hello, Windows IoT!' ›</a>

## Lab 02: Nightlight
In this lab, you will capture analog input using a light-dependent resistor (photocell) and turn on or off an LED based on the amount of light detected. 

<a class="radius button small" href="{{ site.url }}/workshop/thingy-4-windows/nightlight/">Estimated time to complete: 20-minutes. Go to  'Lab 02: Nightlight' ›</a>

## Lab 03: ThingLabs Thingy&trade;
In this lab, you will build a device with multiple sensors and actuators. The device will:

* Capture the amount of ambient light
* Increase the brightness intensity of an LED inversely to the amount of ambient light measured.
* Display the ambient light and sound measurements on an LCD display.
* The display will adjust its backlight brightness according to the amount of ambient light in the area.
* Enable a button to turn on and off an LED and trigger sound.

<a class="radius button small" href="{{ site.url }}/workshop/thingy-4-windows/thingy/">Estimated time to complete: 30-minutes. Go to  'Lab 03: Thinglabs Thingy' ›</a>

## Lab 04: Setting Up an Azure IoT Hub
In this lab you will provision a new Azure IoT Hub. Once you have the IoT Hub created, you will be able to create a new Azure IoT device that you will use to send telemetry to Azure. Azure IoT devices are software references to physical devices.

<a class="radius button small" href="{{ site.url }}/workshop/thingy-4-windows/setup-azure-iot-hub/">Estimated time to complete: 20-minutes. Go to  'Lab 04: Setting Up Azure IoT Hub' ›</a>

## Lab 05: Sending Device-to-Cloud (D2C) Messages
In this lab, you will use the ThingLabs Thingy&trade; device that you built to capture and send messages to an Azure IoT Hub.

<a class="radius button small" href="{{ site.url }}/workshop/thingy-4-windows/sending-d2c-messages/">Estimated time to complete: 20-minutes. Go to  'Lab 05: Sending Device-to-Cloud (D2C) Messages' ›</a>

## Lab 06: Storing and Displaying IoT Data
In this lab, you will build a data pipeline that captures the data coming into an IoT Hub. The pipeline will then process the data with Azure Stream Analytics, and then route it to downstream services. You will also create one of those downstream services. Your service will consume the data and present it through an Azure Web App where it will be rendered as a real-time graph.

<a class="radius button small" href="{{ site.url }}/workshop/thingy-4-windows/storing-displaying-data/">Estimated time to complete: 20-minutes. Go to  'Lab 06: Storing & Displaying IoT Data' ›</a>

## Lab 07: Lab: Sending Cloud-to-Device (C2D) Messages
In this lab, you will use the Azure Web App to control the ThingLabs Thingy&trade; remotely. The Web App will send messages to the Thingy via Azure, which the Thingy will receive, procees, and act on.

<a class="radius button small" href="{{ site.url }}/workshop/thingy-4-windows/sending-c2d-messages/">Estimated time to complete: 20-minutes. Go to  'Lab 07: Sending Cloud to Device (C2D) Messages' ›</a>

# Workshop Source Files
If you want access to the completed projects used in the labs, you can copy or clone the __Thingy4Windows__ Github repo [here](https://github.com/ThingLabsIo/Thingy4Windows).
