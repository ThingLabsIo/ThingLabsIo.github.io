---
layout: "page-fullwidth"
title: "Windows 10 & Azure IoT Full Day Workshop"
subheadline: "Building Connected Things with Windows 10 IoT and Microsoft Azure"
teaser: "In these IoT Labs you will begin by learning the basics of working with Windows 10 IoT Core connected to sensors and devices. You will move on to connecting the device to the Cloud. You will learn how to leverage Microsoft Azure services to collect data and control devices and use advanced services like analytics and machine learningto discover insights using your Things."
show_meta: true
comments: false
header: 
    image_fullwidth: "header_unsplash_10.jpg"
breadcrumb: true
categories: [rpi, iot, maker, azure-iot]
permalink: /workshop/fullday-windows/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

# Goals
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

# Outline

## Getting Started (pre-workshop if possible)

## Welcome to the Internet of Things

### IoT Patterns for D2C and C2D

### Maker Hardware Landscape

### Prototyping Hardware

### Lab: Hello, Windows IoT
In this lab you will build a simple single-circuit device that blinks an LED on and off (this is the 'Hello, World! of the maker/IoT space).

## Input/Output

### GPIO

### Pulse Width Modulation

### SPI and I2C

### Lab: Nightlight
In this lab you will capture analog input using a light-dependent resistor (photo cell) and turn on or off an LED based on the amount of light detected.

## Lab: Not Quite the Nest
In this lab you will build a prototype of a device similar to, but not quite, the Nest thermostat. The device will:

+ Captures temerature and humidity and display it on either an LCD or a connected monitor
+ Enable input to alter the desired temperature
+ Turn on a RED light to indicate heating and a blue LED to indicate cooling
+ Stretch: Detects when someone approaches and comes out of sleep-mode

## Lunch

## Introduction to Azure for IoT

## Device-to-Cloud (D2C)

### Lab: Sending Device-to-Cloud Messages
In this lab you will use the thermastat device that you built to capture and send telemetry data to Azure IoT Hub.

## IoT Data Pipelines

### Lab: Storing and Displaying IoT Data
In this lab you will build a data pipeline that captures the data coming into IoT Hub and persists it using Azure storage. You will then connect a website to Azure and retreive the data for display in a browser.

## Coud-to-Device (C2D)

### Lab: Sending Cloud-to-Device Messages
In this lab you will extend your website by adding the ability to control the thermastat remotely. The website will send messages to the thermastat via Azure.

## Wrap Up

 