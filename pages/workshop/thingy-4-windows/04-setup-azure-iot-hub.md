---
layout: "page-fullwidth"
title: "Setting Up Azure IoT"
subheadline: "Building Connected Things with Windows 10 IoT and Microsoft Azure"
teaser: "In this lab, you will provision an Azure IoT Hub and an IoT Hub device."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [raspberry-pi, windows-10, grove, c#, iot, maker, azure, azure-iot-hub]
permalink: /workshop/thingy-4-windows/setup-azure-iot-hub/
---
### Table of Contents
*  Auto generated table of contents
{:toc}

In this lab, you will provision a new Azure IoT Hub. Once you have the IoT Hub created, you will be able to create a new Azure IoT device (a software reference to your physical device) that you will use to send telemetry to Azure.

## Bill of Materials
What you will need:

1. An Azure Account.

## Setup an Azure IoT Hub
In a browser, navigate to the Azure Portal at [https://portal.azure.com](https://portal.azure.com). Login to the account you created in the ['Getting Started' lab](../getting-started/). Once logged in:

1. Click on the __New__ menu option in the upper-left
2. Select __Internet of Things__
3. Select __Azure IoT Hub__
4. Give it a name such as your name followed by _iot-labs_ (e.g. rickgrimes-iot-labs)
5. Select or create a new __Resource Group__
6. Select a location (choose the one closest to your physical location)

![New IoT Hub]({{site.url}}/images/rpi2/rpi2_New-IoT-Hub.png)
  
Once the IoT Hub is created, navigate into it and:

1. Click on the _key_ icon at the top of the blade
2. In the next blade, click on the __iothubowner__ entry
3. Copy the __Connection string-primary key__ to your clipboard

![Azure IoT Hub Connection String]({{site.url}}/images/rpi2/rpi2_AzureIoTConnectionString.png)

## Install Azure IoT Hub DeviceExplorer
Azure IoT Hub only allows connections from known devices that present proper credentials. In this lab series you will use either the _DeviceExplorer_ utility or the _iothub-explorer_ command line interface to provision a device for use in Azure IoT Hub. While Azure IoT Hub supports multiple authentication schemes, you will use pre-shared keys in this lab series.

The simplest way to provision a new device is with the _DeviceExplorer_ utility (Windows only). If you are using Windows, download and run [Device Explorer][deviceexplorer]. After running the installer, the _DeviceExplorer.exe_ can be found at __C:\Program Files (x86)\Microsoft\DeviceExplorer__. When you run the utility you need to input the _iothubowner_ connection strong (from the previous step) in the _IoT Hub Connection String_ field found in the _Configuration_ tab.

![Device Explorer]({{site.url}}/images/rpi2/rpi2_deviceexplorer01.png)

## Create a New Azure IoT Device
If you are using the _DeviceExplorer_ simply open the _Management_ tab and click the _Create_ button. In the dialog that opens, enter the name of your device as your name followed by _Hub_ - e.g. _RickGrimesHub_. Then Click the _Create_ button, and click _Done_ on the confirmation dialog that opens.

![Create a Device]({{site.url}}/images/rpi2/rpi2_deviceexplorer02.png)

You will see your device in the _Devices_ list. Once a device is created, you can get the device-specific connection string by selecting it in the _Devices_ list, right-clicking and selecting _Copy connection string for selected device_:

![Device Connection String]({{site.url}}/images/rpi2/rpi2_deviceexplorer03.png)

## Conclusion &amp; Next Steps
Congratulations! You have created an Azure IoT Hub that you will connect devices to. You also created a representation of a physical device in your IoT Hub. In the [next lab][nextlab] you will build a Universal Windows Application application that will collect data from the RPi2 and send it to Azure IoT Hub.

<a class="radius button small" href="{{ site.url }}/workshop/thingy-4-windows/sending-d2c-messages/">Go to 'Sending Device-to-Cloud (D2C) Messages' ›</a>

[nextlab]: ../sending-d2c-messages/
[deviceexplorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md
