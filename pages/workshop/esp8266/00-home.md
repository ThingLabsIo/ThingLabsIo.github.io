---
layout: "page-fullwidth"
title: "ESP8266 Workshop"
subheadline: "Building Connected Things with an ESP8266 and Microsoft Azure"
teaser: "In this series of Internet of Things (IoT) labs, you will build a Thing connected to sensors and actuators, connect that Thing to the Internet, and leverage the Cloud (Microsoft Azure) to collect data, control devices, and use advanced services like analytics and machine learning to discover insights using your Thing."
show_meta: true
comments: false
header: 
    image_fullwidth: workshops/thingy-4-windows/header.jpeg
breadcrumb: true
categories: [esp8266, lua, MQTT, iot, maker]
permalink: /workshop/esp8266/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

# Workshop Goals
The goals of this workshop are to:

+ Educate developers on the IoT
+ Educate developers on the Azure services related to IoT

By the end of the workshop you will be able to:

+ Build an IoT device using the ESP8266 WiFi module
+ Write a Lua program that runs on the ESP8266
+ Use Lua on the ESP8266 to both capture input and display output
+ Create and configure an Azure IoT Hub to manage device-to-cloud and cloud-to-device messaging
+ Connect a Lua application running on the ESP8266 to an Azure IoT Hub
+ Build a data pipeline that captures and stores data coming into an IoT Hub
+ Build a visualization of IoT data
+ Build a client application that can send a command to the IoT device via Azure

# Prep Work

## Getting Started
This is a step-by-step guide to preparing your computer for this workshop.

<a class="radius button small" href="{{ site.url }}/workshop/esp8266/getting-started/">Estimated time to complete: 20-minutes. Go to  'Getting Started' ›</a>

# Lab Guides

## Lab 01: Hello, Lua IoT!
In this lab, you will build a simple single-circuit device that blinks an LED on and off. (This is the ‘Hello, World! of the IoT space).

<a class="radius button small" href="{{ site.url }}/workshop/esp8266/hello-lua-iot/">Estimated time to complete: 20-minutes. Go to  'Lab 01: Hello, Lua IoT!' ›</a>

## Lab 02: Nightlight
In this lab, you will capture analog input using a light-dependent resistor (photocell) and turn on or off an LED based on the amount of light detected. 

<a class="radius button small" href="{{ site.url }}/workshop/esp8266/nightlight/">Estimated time to complete: 20-minutes. Go to  'Lab 02: Nightlight' ›</a>

## Lab 03: Thinglabs Weather Station
In this lab, you will add a temperature & humidity sensor, the DHT22, to your ESP8266 to build a minimal weather station.

<a class="radius button small" href="{{ site.url }}/workshop/esp8266/weather/">Estimated time to complete: 20-minutes. Go to  'Lab 03: Thinglabs Weather Station' ›</a>

## Lab 04: Setting Up Azure IoT Hub
In this lab, you will provision a new Azure IoT Hub. Creating and provisioning an IoT Hub will allow you to create new Azure IoT devices (a software reference to your physical device) that can send telemetry data to Azure.

<a class="radius button small" href="{{ site.url }}/workshop/esp8266/setup-azure-iot-hub/">Estimated time to complete: 20-minutes. Go to  'Lab 04: Setting Up Azure IoT Hub' ›</a>

## Lab 05: Sending Device-to-Cloud (D2C) Messages
In this lab, you will use the Thinglabs Weather Station that you built in Lab 03 to capture and send messages to your Azure IoT Hub.

<a class="radius button small" href="{{ site.url }}/workshop/esp8266/sending-d2c-messages/">Estimated time to complete: 20-minutes. Go to  'Lab 05: Sending Telemetry to the Cloud' ›</a>

## Lab 06: Storing and Displaying IoT Data
In this lab, you will build a data pipeline that captures the data coming into your Azure IoT Hub, processes the data it with Azure Stream Analytics, and then routes the data to downstream services. You will also create one of those downstream services. Your service will consume the data and present it through an Azure Web App where it will be rendered as a real-time graph.

<a class="radius button small" href="{{ site.url }}/workshop/esp8266/storing-displaying-data/">Estimated time to complete: 20-minutes. Go to  'Lab 06: Storing & Displaying IoT Data' ›</a>

## Lab 07: Sending Cloud-to-Device (C2D) Messages
In this lab, you will use the Azure Web App from Lab 06 to control the ThingLabs Weather Station remotely. The Web App will send messages to the Weather Station via Azure, which the Weather Station will receive, process, and act upon.

<a class="radius button small" href="{{ site.url }}/workshop/esp8266/sending-c2d-messages/">Estimated time to complete: 20-minutes. Go to  'Lab 07: Sending Cloud to Device (C2D) Messages' ›</a>

