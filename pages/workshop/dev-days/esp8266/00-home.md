---
layout: "page-fullwidth"
title: "ESP8266 Workshop"
subheadline: "Building Connected Things with an ESP8266 and Microsoft Azure"
teaser: "In these IoT labs, you will learning to build a Thing connected to sensors. You will move on to connecting the Thing to the Internet. You will learn how to leverage the Cloud (Microsoft Azure) to collect data that can be used within advanced services like analytics and machine learning to discover insights."
show_meta: true
comments: false
header: 
    image_fullwidth: workshops/thingy-4-windows/header.jpeg
breadcrumb: true
categories: [esp8266, lua, MQTT, iot, maker]
permalink: /workshop/dev-days/esp8266/
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

# Lab Guides

## Lab 01: Hello, Lua IoT!
In this lab, you will build a simple single-circuit device that blinks an LED on and off (this is the ‘Hello, World! of the maker/IoT space).

<a class="radius button small" href="{{ site.url }}/workshop/dev-days/esp8266/hello-lua-iot/">Hello, Lua IoT!</a>

## Lab 02: Weather Station
In this lab, you will add a DHT22 to your ESP8266 and capture temperature and humidity to build a minimal weather station.

<a class="radius button small" href="{{ site.url }}/workshop/dev-days/esp8266/weather/">Weather Station</a>

## Lab 03: Setting Up Azure IoT Hub
In this lab, you will provision a new Azure IoT Hub. Once you have the IoT Hub created, you will be able to create a new Azure IoT device (a software reference to your physical device) that you will use to send telemetry to Azure.

<a class="radius button small" href="{{ site.url }}/workshop/dev-days/esp8266/setup-azure-iot-hub/">Setting Up Azure IoT Hub</a>

## Lab 04: Sending Device-to-Cloud (D2C) Messages
In this lab, you will use the ThingLabs Thingy&trade; device that you built to capture and send messages to Azure IoT Hub.

<a class="radius button small" href="{{ site.url }}/workshop/dev-days/esp8266/sending-d2c-messages/">Sending Telemetry to the Cloud</a>

## Lab 05: Storing and Displaying IoT Data
In this lab, you will build a data pipeline that captures the data coming into IoT Hub, processes it with Azure Stream Analytics, then routes it to downstream services. The first service you'll build will consume the data and present it through an Azure Web App where it is rendered as a real-time graph.

<a class="radius button small" href="{{ site.url }}/workshop/dev-days/esp8266/storing-displaying-data/">Storing & Displaying IoT Data</a>

