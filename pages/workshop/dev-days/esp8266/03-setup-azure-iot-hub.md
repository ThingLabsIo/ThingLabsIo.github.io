---
layout: "page-fullwidth"
title: "Setting Up Azure IoT"
subheadline: "Building Connected Things with an ESP8266 and Microsoft Azure"
teaser: "In this lab, you will provision an Azure IoT Hub and an IoT Hub device."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [esp8266, lua, MQTT, iot, maker, azure, azure-iot-hub]
permalink: /workshop/dev-days/esp8266/setup-azure-iot-hub/
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

# Create a New Azure IoT Device using DeviceExplorer
{% include azure/new-device-deviceexplorer.md %}

For more information on how to use DeviceExplorer, the Project [deviceexplorer](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) is available on Github.

# Conclusion &amp; Next Steps
Congratulations! You have created an Azure IoT Hub that you will connect devices to. You also created a representation of a physical device in your IoT Hub. 

In the [next lab][nextlab], you'll deploy an Azure Web Site that uses D3.js to visualize the output of your Weather Station.

<a class="radius button small" href="{{ site.url }}/workshop/dev-days/esp8266/sending-d2c-messages/">Go to 'Sending Device-to-Cloud (D2C) Messages' ›</a>

[nextlab]: /workshop/dev-days/esp8266/sending-d2c-messages/