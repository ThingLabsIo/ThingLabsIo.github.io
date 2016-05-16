---
layout: page-fullwidth
title: "Setting Up and Azure IoT Hub"
subheadline: "Building Connected Things with Node.js, Johnny-Five, and Microsoft Azure"
teaser: "In this lab, you will provision an Azure IoT Hub and an IoT Hub device."
show_meta: true
comments: true
header: no
breadcrumb: true
categories: [edison, iot, maker, javascript, node.js, johnny-five]
permalink: /labs/edison/grove/setup-iot-hub/
---
# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab, you will provision a new Azure IoT Hub. Once you have the IoT Hub created, you will be able to create a new Azure IoT device (a software reference to your physical device) that you will use to send telemetry to Azure.

# Bill of Materials
What you will need:

1. An Azure Account.

# Setup an Azure IoT Hub
{% include azure/setup-iot-hub.md %}

# Install Azure IoT Hub DeviceExplorer (Windows Only)

__Non-Windows Users - advance to the next heading.__

{% include azure/install-device-explorer.md %}

# Optional: Install the IoT Hub Explorer Command Line Interface
{% include azure/install-iothub-explorer.md %}

# Create a New Azure IoT Device with the DeviceExplorer
{% include azure/new-device-deviceexplorer.md %}

# Create a New Azure IoT Device with the IoTHub-Explorer
{% include azure/new-device-iothubexplorer.md %}

# Conclusion &amp; Next Steps
Congratulations! You have created an Azure IoT Hub that you will connect devices to. You also created a representation of a physical device in your IoT Hub. 

{% include edison/nextlab title='Sending D2C Messages' url='../d2c/' %}