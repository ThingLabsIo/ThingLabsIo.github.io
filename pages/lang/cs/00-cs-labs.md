---
layout: "page-cs"
title: "Windows 10 IoT and C# Labs"
subheadline: "Building Connected Things with Windows 10 IoT and C#"
teaser: "In these IoT Labs you will begin by learning the basics of working with Windows 10 IoT Core connected to sensors and devices. You will move on to connecting the device to the Cloud. You will learn how to leverage Microsoft Azure services to collect data and control devices and use advanced services like analytics and machine learningto discover insights using your Things."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [rpi, dragon, minnowboard, maker]
permalink: /lang/cs/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

# Getting Started
This is a step-by-step guide to preparing your computer for the IoT labs using JavaScript, Node.js and Johnny-Five.

<a class="radius button small" href="{{ site.url }}/lang/cs/getting-started/">Estimated time to complete: 20-minutes. Go to  'Getting Started' ›</a>

# Thing 101 Labs

## Setting Up Your Device
In this lab you will install firmware onto your board that will enable communication with the gateway application. How you configure the board and what firmware you install will depend on the board you choose to use.

### Raspberry Pi 2
The Raspberry Pi 2 is a low cost, credit-card sized computer that plugs into a computer monitor or TV, and uses a standard keyboard and mouse. The Raspberry Pi 2 runs Windows 10 IoT Core.

In this lab you will install Windows 10 IoT Core on your Raspberry Pi 2.

<a class="radius button small" href="{{ site.url }}/lang/cs/setup-rpi2/">Estimated time to complete: 20-minutes. Go to  'Setting Up Your Raspberry Pi 2' ›</a>

### DragonBoard 410c
The DragonBoard™ 410c based on Linaro 96Boards™ specification features the Qualcomm® Snapdragon™ 410 processor, a Quad-core ARM® Cortex™ A53 at up to 1.2GHz clock speed per core, capable of 32-bit and 64-bit operation. WLAN, Bluetooth, and GPS, all packed into a board the size of a credit card.

In this lab you will install Windows 10 IoT Core on your DragonBoard 410c.

<a class="radius button small" href="{{ site.url }}/lang/cs/setup-dragon/">Estimated time to complete: 20-minutes. Go to  'Setting Up Your Dragon Board' ›</a>

## Writing Digital Output
In this lab you will use one of the digital output pins to send a signal to an LED.

<a class="radius button small" href="{{ site.url }}/lang/cs/writing-digital-output/">Estimated time to complete: 20-minutes. Go to  'Writing Digital Output' ›</a>

## Reading Analog Input
In this lab you will read input from an analog sensor using a voltage divider.

<a class="radius button small" href="{{ site.url }}/lang/cs/reading-analog-input/">Estimated time to complete: 20-minutes. Go to  'Reading Analog Input' ›</a>

## Input Controls Output
In this lab you will use analog input to control digital output.

<a class="radius button small" href="{{ site.url }}/lang/cs/input-controls-output/">Estimated time to complete: 20-minutes. Go to  'Input Controls Output' ›</a>

# Connected Thing Labs
There are options for the Connected Things Labs when coding with JavaScript depending on the board you are working with and the sensors, devices or shields you have available. Regardless of which option you pursue, you must first setup your Microsoft Azure IoT Hub. Once you have done that, return to this page to select the next lab based on your hardware.

## Setting Up Azure IoT Hub
In this lab you will provision a new Azure IoT Hub. Once you have the IoT Hub created, you will be able to create a new Azure IoT device (a software reference to your physical device) that you will use to send telemetry to Azure.

<a class="radius button small" href="{{ site.url }}/lang/cs/setup-azure-iot-hub/">Estimated time to complete: 20-minutes. Go to  'Setting Up Azure IoT Hub' ›</a>

## Sending Telemetry to the Cloud
In this lab you will write a Node.js application that runs on a hub (your development machine) and collects data from a development board and sends it up to your Azure IoT Hub.

<a class="radius button small" href="{{ site.url }}/lang/cs/sending-telemetry/">Estimated time to complete: 20-minutes. Go to  'Sending Telemetry to the Cloud' ›</a>

# Cloud IoT Service Labs
In these labs you will learn how to use Cloud services to gain insights and derive value from the data you are collecting from your _Thing_.

## Visualizing IoT Data with Power BI
In this lab you will create visualizations of IoT data using Microsoft Power BI.

<a class="radius button small" href="{{ site.url }}/lang/cs/visualize-iot-with-powerbi/">Estimated time to complete: 20-minutes. Go to  'Visualizing IoT Data with Power BI' ›</a>