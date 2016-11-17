---
layout: "page-fullwidth"
title: "ESP8266 Workshop"
subheadline: "Building Connected Things with an ESP8266 and Microsoft Azure"
teaser: "In these IoT labs, you will learning to build a Thing connected to sensors and actuators. You will move on to connecting the Thing to the Internet. You will learn how to leverage the Cloud (Microsoft Azure) to collect data and control devices and use advanced services like analytics and machine learning to discover insights using your Things."
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
The goals of this workshop are:

+ Educate developers on the Internet of Things (IoT)
+ Educate developers on the Azure services related to IoT

By the end of the workshop a participant will be able to:

+ Build an IoT device using the ESP8266
+ Write a Lua program that runs on the ESP8266
+ Use Lua on the ESP8266 to both capture input and present output
+ Create and configure an Azure IoT Hub to manage device-to-cloud and cloud-to-device messaging
+ Connect a Lua application running on the ESP8266 to an Azure IoT Hub
+ Build a data pipeline that captures data coming into IoT Hub and stores it
+ Build a visualization of IoT data
+ Build a client application that can send a command to the IoT device via Azure

# Prep Work
The following are required to prepare for the workshop. Whenever possible this section should be completed prior to the workshop day.

## Getting Started
This is a step-by-step guide to preparing your computer for the ThingLabs Thingy IoT Workshop: ESP8266 Edition.

<a class="radius button small" href="{{ site.url }}/workshop/esp8266/getting-started/">Estimated time to complete: 20-minutes. Go to  'Getting Started' ›</a>

# Lab Guides

## Lab 01: Hello, Lua IoT!
In this lab, you will build a simple single-circuit device that blinks an LED on and off (this is the ‘Hello, World! of the maker/IoT space).

<a class="radius button small" href="{{ site.url }}/workshop/esp8266/hello-lua-iot/">Estimated time to complete: 20-minutes. Go to  'Hello, Lua IoT!' ›</a>

## Lab 02: Nightlight
In this lab, you will capture analog input using a light-dependent resistor (photocell) and turn on or off an LED based on the amount of light detected. 

<a class="radius button small" href="{{ site.url }}/workshop/esp8266/nightlight/">Estimated time to complete: 20-minutes. Go to  'Nightlight' ›</a>

## Lab 03: Weather Station
In this lab, you will add a DHT22 to your ESP8266 and capture temperature and humidity to build a minimal weather station.

<a class="radius button small" href="{{ site.url }}/workshop/esp8266/weather/">Estimated time to complete: 20-minutes. Go to  'Weather Station' ›</a>

## Lab 04: Setting Up Azure IoT Hub
In this lab, you will provision a new Azure IoT Hub. Once you have the IoT Hub created, you will be able to create a new Azure IoT device (a software reference to your physical device) that you will use to send telemetry to Azure.

<a class="radius button small" href="{{ site.url }}/workshop/esp8266/setup-azure-iot-hub/">Estimated time to complete: 20-minutes. Go to  'Setting Up Azure IoT Hub' ›</a>

## Lab 05: Sending Device-to-Cloud (D2C) Messages
In this lab, you will use the ThingLabs Thingy&trade; device that you built to capture and send messages to Azure IoT Hub.

<a class="radius button small" href="{{ site.url }}/workshop/esp8266/sending-d2c-messages/">Estimated time to complete: 20-minutes. Go to  'Sending Telemetry to the Cloud' ›</a>

## Lab 06: Storing and Displaying IoT Data
In this lab, you will build a data pipeline that captures the data coming into IoT Hub, processes it with Azure Stream Analytics, then routes it to downstream services. The first service you'll build will consume the data and present it through an Azure Web App where it is rendered as a real-time graph.

<a class="radius button small" href="{{ site.url }}/workshop/esp8266/storing-displaying-data/">Estimated time to complete: 20-minutes. Go to  'Storing & Displaying IoT Data' ›</a>

## Lab 07: Lab: Sending Cloud-to-Device (C2D) Messages
In this lab, you will use the Azure Web App to control the ThingLabs Thingy&trade; remotely. The Web App will send messages to the Thingy via Azure, which the Thingy will receive, procees, and act on.

<a class="radius button small" href="{{ site.url }}/workshop/esp8266/sending-c2d-messages/">Estimated time to complete: 20-minutes. Go to  'Sending Cloud to Device (C2D) Messages' ›</a>

