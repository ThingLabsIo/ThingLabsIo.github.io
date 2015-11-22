---
layout: page-fullwidth
title: "Azure IoT Connected Weather Station"
subheadline: "Microsoft Azure IoT Labs"
teaser: "A series of labs that teach you to connect physical devices to Azure IoT Hubs."
show_meta: true
comments: true
header: no
breadcrumb: true
categories:
    - iot-labs
    - iot-arduino-labs
    - iot-photon-labs
    - azure-iot
    - maker-101
author: doug_seven
permalink: "/azure/"
---
### Table of Contents
*  Auto generated table of contents
{:toc}

The labs in this series build on each other to enable you to prototype your own Internet of Things (IoT) devices. In this lab you will build a solution using a 'Gateway' pattern, where a device (the Gateway) collects data from one or more connected devices/sensors and aggregates the data before sending it to the cloud. For the gateway, you will write a Node.js application that uses an open source framework for interacting with hardware, called [Johnny-Five][j5], which works as a baseline control kit for hardware projects, including the Arduino, Raspberry Pi, and Particle Photon boards. This enables you to write applications in JavaScript that can run as a gateway either on your computer or on a hub device (like an Arduino Y&uacute;n, Raspberry Pi 2, or Intel Edison) connected to an variety of sensors and devices.

<img src="/images/gatewaypattern.png"/>

If you want to deploy the applications you build to a gateway device, you will need a device that is capable of running Node.js, such as the Raspberry Pi 2, Arduino Y&uacute;n or the Linino ONE.

## Getting Started
This is a step-by-step guide to preparing your development computer (Windows or MacOS X) for the Azure IoT Labs.

Estimated time to complete: 20-minutes

[Getting Started =>][getting-started]

## Setting Up Azure IoT Hub
In this lab you will provision an Azure IoT Hub and provision an Azure IoT device.

Estimated time to complete: 20-minutes

[Setting Up Azure IoT and Your Device =>][setup-azure-iot-hub]

## Preparing Your Device
Depending on the device you are using, there are slight differences in how you prepare them and set them up. 

[Option 1: Preparing Your Arduino =>][setup-arduino]

Estimated time to complete: 20-minutes

[Option 2: Preparing Your Particle Photon =>][setup-photon]

Estimated time to complete: 30-minutes

## Sending Telemetry to the Cloud
In this lab you will write a Node.js application to collect sensor readings from a _Thing_ and send the data as messages to your Azure IoT Hub.

Estimated time to complete: 30-minutes

[Sending Telemetry to the Cloud =>][sending-telemetry]

## Visualizing IoT Data
In this lab you will create an Azure Stream Analytics job to query data coming in to your Azure IoT hub and stream it out to Power BI. In Power BI you will build visualizations of your IoT data.

Estimated time to complete: 30-minutes

[Visualizing IoT Data =>][visualize-iot-with-powerbi]

## Coming Soon
More labs will be coming soon to teach you how to send command and control messages to your devices, how to deploy and run the Node.js app from a Raspberry Pi, and how to build a client app that interacts with your IoT devices.

[getting-started]: /getting-started
[setup-azure-iot-hub]: /setup-azure-iot-hub
[setup-photon]: /setup-photon
[setup-arduino]: /setup-arduino
[sending-telemetry]: /sending-telemetry
[visualize-iot-with-powerbi]: /visualize-iot-with-powerbi
[j5]: http://johnny-five.io