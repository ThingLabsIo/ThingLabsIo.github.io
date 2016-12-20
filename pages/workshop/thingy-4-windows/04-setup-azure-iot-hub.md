---
layout: "page-fullwidth"
title: "Lab 04: Setting Up an Azure IoT"
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

In this lab you will provision a new Azure IoT Hub. Once you have the IoT Hub created, you will be able to create a new Azure IoT device that you will use to send telemetry to Azure. Azure IoT devices are software references to physical devices.

## Bill of Materials
What you will need:

1. An Azure Account.

## Setup an Azure IoT Hub
In a browser, navigate to the Azure Portal at [https://portal.azure.com](https://portal.azure.com). Login to the account you created in the ['Getting Started' lab](../getting-started/). Once logged in:

1. Click on the __New__ menu option in the upper-left.
2. Select __Internet of Things__.
3. Select __Azure IoT Hub__.
4. Give it a name. (e.g. your name followed by iot-labs)
5. Select or create a new __Resource Group__.
6. Select a location. Choose the one closest to your physical location.

![New IoT Hub]({{site.url}}/images/rpi2/rpi2_New-IoT-Hub.png)
  
Once the IoT Hub is created,

1. Navigate into the IoT Hub.
2. Click on the _key_ icon at the top of the blade.
3. In the next blade, click on the __iothubowner__ entry.
4. Copy the __Connection string-primary key__ to your clipboard.

![Azure IoT Hub Connection String]({{site.url}}/images/rpi2/rpi2_AzureIoTConnectionString.png)

Azure IoT Hub only allows connections from known devices that present proper credentials. In this lab you will use either the _DeviceExplorer_ utility or the _iothub-explorer_ command line interface to provision a device for use in Azure IoT Hub. Although Azure IoT Hub supports multiple authentication schemes, you will use pre-shared keys in this lab.

## Option 1: Use Azure IoT Hub DeviceExplorer (Windows Only)
The simplest way to provision a new device is with the _DeviceExplorer_ utility. _DeviceExplorer_ is only available in Windows. 

1. Download and run [Device Explorer][deviceexplorer]{:target="_blank"}. 
2. After running the installer, the _DeviceExplorer.exe_ can be found at __C:\Program Files (x86)\Microsoft\DeviceExplorer__. 
3. Run _DevicExplorer.exe_.
4. Open the _Configuration_ tab.
5. Paste the _iothubowner_ connection string from the previous section of this lab in the _IoT Hub Connection String_ field.
6. Click __Update__.

![DeviceExplorer](/images/rpi2/rpi2_deviceexplorer01.png)

You'll now create a new Azure IoT Device.

1. Switch to the _Management_ tab.
3. Click the _Create_ button.
4. In the dialog that opens, enter a name for your IoT device. This can be the same name as your Raspberry Pi 2, or it can be different.
5. Click the _Create_ button, and click _Done_ on the confirmation dialog that opens.

![Create a new virtual device](/images/rpi2/rpi2_deviceexplorer02.png) 

You will see your device in the _Devices_ list. Once a device is created, you can get the device-specific connection string.

1. Select your device in the _Devices_ list
2. Right-click and select __Copy connection string for selected device__:

![Get the device-specific connection string](/images/rpi2/rpi2_deviceexplorer03.png) 

## Option 2: Use the IoT Hub Explorer Command Line Interface (Cross Platform)
If you're not using Windows, or if you prefer to use a command line interface, you can install the _iothub-explorer_ command line interface. The iothub-explorer tool enables you to provision devices in your IoT hub. It runs on any computer where Node.js is available. You can install Node.js from [NodeJS.org](https://nodejs.org){:target="_blank"}.

1. Open a command prompt and type the following:

<pre>
  npm install -g iothub-explorer
</pre>

2. After _iothub-explorer_ is installed, execute the following, replacing <code>[connection-string]</code> with your <i>iothubowner</i> connection string (from the previous lab) and <code>[device-id]</code> with the Azure IoT Hub id for this device.

<pre>
  iothub-explorer [YOUR IOT HUB CONNECTION STRING] create [YOUR DEVICE NAME] --connection-string
</pre>

3. Copy the device-specific connection string.

## Conclusion &amp; Next Steps
Congratulations! You have created an Azure IoT Hub that you will connect devices to. You also created a representation of a physical device in your IoT Hub. In the [next lab][nextlab] you will build a Universal Windows Application application that will collect data from the Raspberry Pi 2 and send it to Azure IoT Hub.

<a class="radius button small" href="{{ site.url }}/workshop/thingy-4-windows/sending-d2c-messages/">Go to 'Lab 05: Sending Device-to-Cloud (D2C) Messages' ›</a>

[nextlab]: ../sending-d2c-messages/
[deviceexplorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md
