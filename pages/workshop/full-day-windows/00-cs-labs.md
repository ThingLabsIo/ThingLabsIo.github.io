---
layout: "page-full-day-windows"
title: "ThingLabs Full Day Workshop"
subheadline: "Building Connected Things with Windows 10 IoT and Microsoft Azure"
teaser: "In these IoT Labs you will begin by learning the basics of working with Windows 10 IoT Core connected to sensors and devices. You will move on to connecting the device to the Cloud. You will learn how to leverage Microsoft Azure services to collect data and control devices and use advanced services like analytics and machine learningto discover insights using your Things."
show_meta: true
comments: false
header: 
    image_fullwidth: workshops/full-day-windows/header.jpg
breadcrumb: true
categories: [rpi, dragon, minnowboard, maker]
permalink: /workshop/full-day-windows/home/
---

# Table of Contents
*  Auto generated table of contents
{:toc}


# Workshop Goals
The goals of this workshop are:

+ Educate developers on the IoT stack offerred by Microsoft
+ Educate developers on the Universal Windows Platform (UWP)
+ Educate developers on the Azure services related to IoT

By the end of the workshop a participant will be able to:

+ Build an IoT device using the Raspberry Pi 2 (RPi2)
+ Build a UWP app for Windows IoT Core to run on the RPi2
+ Use Windows IoT Core and the RPi to both capture input and present output
+ Create and configure an Azure IoT Hub
+ Connect their UWP application running on the RPi2 to Azure IoT Hubs
+ Build a datapipe line that captures data coming into IoT Hub and stores it
+ Build a visualization of IoT data
+ Build a client application that can send a command to the IoT device via Azure

# Prep Work
The following are required to prepare for the workshop. Whenever possible this section should be completed prior to the workshop day.

## Getting Started
This is a step-by-step guide to preparing your computer for the Windows 10 IoT Labs.

<a class="radius button small" href="{{ site.url }}/workshop/full-day-windows/getting-started/">Estimated time to complete: 20-minutes. Go to  'Getting Started' ›</a>

## Setting Up Your Raspberry Pi 2
The Raspberry Pi 2 is a low cost, credit-card sized computer that plugs into a computer monitor or TV, and uses a standard keyboard and mouse. The Raspberry Pi 2 runs Windows 10 IoT Core.

In this lab you will install Windows 10 IoT Core on your Raspberry Pi 2.

<a class="radius button small" href="{{ site.url }}/workshop/full-day-windows/setup-rpi2/">Estimated time to complete: 20-minutes. Go to  'Setting Up Your Raspberry Pi 2' ›</a>

# Lab Guides

## Lab 01: Hello, Windows IoT!
In this lab you will build a simple single-circuit device that blinks an LED on and off (this is the ‘Hello, World! of the maker/IoT space).

<a class="radius button small" href="{{ site.url }}/workshop/full-day-windows/hello-windows-iot/">Estimated time to complete: 20-minutes. Go to  'Hello, Windows IoT!' ›</a>

## Lab 02: Nightlight
In this lab you will capture analog input using a light-dependent resistor (photo cell) and turn on or off an LED based on the amount of light detected. 

<a class="radius button small" href="{{ site.url }}/workshop/full-day-windows/nightlight/">Estimated time to complete: 20-minutes. Go to  'Nightlight' ›</a>

## Lab 03: ThingLabs Thingy&trade;
In this lab you will build a device with multiple sensors and actuators. The device will:

* Capture the amount of ambient light
* Increase the brightness intensity of an LED inversely to the amount of ambient light measured.
* Capture the amount of ambient  sound
* Display the ambient light and sound measurments on an LCD display.
* The display will adjust its backlight brightness according to the amount of ambient light in the area.
* Enable a button to turn on and off an LED and trigger sound.

<a class="radius button small" href="{{ site.url }}/workshop/full-day-windows/thingy/">Estimated time to complete: 30-minutes. Go to  'Thingy' ›</a>

## Lab 04: Setting Up Azure IoT Hub
In this lab you will provision a new Azure IoT Hub. Once you have the IoT Hub created, you will be able to create a new Azure IoT device (a software reference to your physical device) that you will use to send telemetry to Azure.

<a class="radius button small" href="{{ site.url }}/workshop/full-day-windows/setup-azure-iot-hub/">Estimated time to complete: 20-minutes. Go to  'Setting Up Azure IoT Hub' ›</a>

## Lab 05: Sending Device-to-Cloud (D2C) Messages
In this lab you will use the thermastat device that you built to capture and send telemetry data to Azure IoT Hub.

<a class="radius button small" href="{{ site.url }}/workshop/full-day-windows/sending-d2c-messages/">Estimated time to complete: 20-minutes. Go to  'Sending Telemetry to the Cloud' ›</a>

## Lab 06: Storing and Displaying IoT Data
In this lab you will build a data pipeline that captures the data coming into IoT Hub and persists it using Azure storage. You will then connect a website to Azure and retreive the data for display in a browser.

## Lab 07: Lab: Sending Cloud-to-Device (C2D) Messages
In this lab you will extend your website by adding the ability to control the THingLabs Thingy&trade; remotely. The website will send messages to the Thingy via Azure.

<a class="radius button small" href="{{ site.url }}/workshop/full-day-windows/visualize-iot-with-powerbi/">Estimated time to complete: 20-minutes. Go to  'Visualizing IoT Data with Power BI' ›</a>