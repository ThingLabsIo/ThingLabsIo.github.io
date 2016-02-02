---
layout: page-fullwidth
title: "Setting Up Azure IoT"
subheadline: "Building Connected Things with Windows 10 IoT Core and C#"
teaser: "In this lab you will provision an Azure IoT Hub and an IoT Hub device."
show_meta: true
comments: true
header: no
breadcrumb: true
categories: [raspberry-pi, windows-10, c#, iot, maker, azure, azure-iot-hub]
permalink: /tr22/cs/setup-azure-iot-hub/
---
### Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will provision a new Azure IoT Hub. Once you have the IoT Hub created, you will be able to create a new Azure IoT device (a software reference to your physical device) that you will use to send telemetry to Azure.

## Setup an Azure IoT Hub
In a browser, navigate to the Azure Portal at [https://portal.azure.com](https://portal.azure.com). Login to your Azure account (if you don't have one, register for a [free trial](https://azure.microsoft.com/en-us/pricing/free-trial/)). Once logged in:

1. Click on the __New__ menu option in the upper-left
2. Select __Internet of Things__
3. Select __Azure IoT Hub__
4. Give it a name such as your name followed by _iot-labs_ (e.g. rickgrimes-iot-labs)
5. Select or create a new __Resource Group__
6. Select a location (choose the one closest to your physical location)

![Create an IoT Hub](/images/rpi2/rpi2_New-IoT-Hub.png)
  
Once the IoT Hub is created, navigate into it and:

1. Click on the _key_ icon at the top of the blade
2. In the next blade, click on the __iothubowner__ entry
3. Copy the __Connection string-primary key__ to your clipboard

![Get the IoT Hub Owner Connection String](/images/rpi2/rpi2_AzureIoTConnectionString.png)

## Use Azure IoT Hub DeviceExplorer
Azure IoT Hub only allows connections from known devices that present proper credentials. In this lab series you will use either the _DeviceExplorer_ utility or the _iothub-explorer_ command line interface to provision a device for use in Azure IoT Hub. While Azure IoT Hub supports multiple authentication schemes, you will use pre-shared keys in this lab series.

The simplest way to provision a new device is with the _DeviceExplorer_ utility (Windows only). The _DeviceExplorer.exe_ can be found at __C:\Program Files (x86)\Microsoft\DeviceExplorer__. When you run the utility you need to input the _iothubowner_ connection strong (from the previous step) in the _IoT Hub Connection String_ field found in the _Configuration_ tab.

![Use DeviceExplorer](/images/rpi2/rpi2_deviceexplorer01.png)

## Create a New Azure IoT Device

1. Open _DeviceExplorer_ 
2. Open the _Management_ tab
3. Click the _Create_ button
4. In the dialog that opens, enter the name of your device - __ThingLabsXX__ where _XX_ is the number on your Raspberry Pi.
5. Click the _Create_ button, and click _Done_ on the confirmation dialog that opens.

![Create a new virtual device](/images/rpi2/rpi2_deviceexplorer02.png) 

You will see your device in the _Devices_ list. Once a device is created, you can get the device-specific connection string by selecting it in the _Devices_ list, right-clicking and selecting _Copy connection string for selected device_:

![Get the device-specific connection string](/images/rpi2/rpi2_deviceexplorer03.png) 

## Conclusion &amp; Next Steps
Congratulations! You have created an Azure IoT Hub that you will connect devices to. You also created a representation of a physical device in your IoT Hub. In the next lab you will build a Universal Windows Application that will collect data from the RPi2 and send it to Azure IoT Hub.

<a class="radius button small" href="{{ site.url }}/tr22/cs/sending-telemetry/">Go to 'Sending Telemetry to the Cloud' ›</a>

[deviceexplorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md
[nextlab]: /tr22/cs/sending-telemetry/