---
layout: page-tr22
title: "Setting Up Azure IoT Hub"
subheadline: "Building Connected Things with Node.js"
teaser: "In this lab you will provision an Azure IoT Hub and an IoT Hub device."
show_meta: true
comments: true
header: no
breadcrumb: true
categories: [azure, iot-hubs, device-explorer, iothub-explorer]
author: doug_seven
permalink: /tr22/js/setup-azure-iot-hub/
---
# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will provision a new Azure IoT Hub. Once you have the IoT Hub created, you will be able to create a new Azure IoT device (a software reference to your physical device) that you will use to send telemetry to Azure. 

# Setup an Azure IoT Hub
In a browser, navigate to the [Azure Portal at http://portal.azure.com](https://portal.azure.com) and login. Once logged in:

1. Click on the _New_ menu option in the upper-left
2. Select _Internet of Things_
3. Select _Azure IoT Hub_
4. Give it a name such as your name followed by 'iot-labs' (i.e. rickgrimes-iot-labs)
5. Select or create a new Resource Group
6. Select a location (choose the one closest to your physical location)

<img src="/images/New-IoT-Hub.png"/>
  
Once the IoT Hub is created, navigate into it and:

1. Click on the key icon at the top of the blade
2. In the next blade, click on the _iothubowner_ entry
3. Copy the _Connection string-primary key_ to your clipboard

<img src="/images/AzureIoTConnectionString.png"/>

# Install Azure IoT Hub DeviceExplorer
Azure IoT Hub only allows connections from known devices that present proper credentials. In this lab series you will use either the _DeviceExplorer_ utility or the _iothub-explorer_ command line interface to provision a device for use in Azure IoT Hub. While Azure IoT Hub supports multiple authentication schemes, you will use pre-shared keys in this lab series.

The simplest way to provision a new device is with the _DeviceExplorer_ utility (Windows only). If you are using Windows, download and run [Device Explorer][deviceexplorer]. After running the installed, the _DeviceExplorer.exe_ can be found at __C:\Program Files (x86)\Microsoft\DeviceExplorer__. When you run the utility you need to input the _iothubowner_ connection strong (from the previous step) in the _IoT Hub Connection String_ field found in the _Configuration_ tab.

<img src="/images/deviceexplorer01.png"/>

## Install the IoT Hub Explorer Command Line Interface
If you are on a non-Windows machine, or prefer to use a command line interface instead of the _DeviceExplorer_ utility, you can install the _iothub-explorer_ command line interface. The iothub-explorer tool enables you to provision devices in your IoT hub. It runs on any computer where Node.js is available.

On Windows, open the Node.js command prompt and type the following:
<pre>
  cd C:\Development\IoTLabs
  npm install -g iothub-explorer
</pre>

On Mac OS X open Terminal and type the following:

<pre>
  cd ~/Development/IoTLabs
  npm install -g iothub-explorer
</pre>

# Create a New Azure IoT Device
If you are using the _DeviceExplorer_ simply open the _Management_ tab and click the _Create_ button. In the dialog that opens, enter the name of your device  - something like __MyIoTGateway__ works well. Then Click the _Create_ button, and click _Done_ on the confirmation dialog that opens.

<img src="/images/deviceexplorer02.png"/>

You will see your device in the _Devices_ list. Once a device is created, you can get the device-specific connection string by selecting it in the _Devices_ list, right-clicking and selecting _Copy connection string for selected device_:

<img src="/images/deviceexplorer03.png"/> 

## Create a New Azure IoT Device from the Command Line
If you are on a non-Windows machine, or prefer to use a command line interface instead of the _DeviceExplorer_ utility, you can provision a new Azure IoT Hub device using the _iothub-explorer_ command line interface.

In the same directory as before, using the Node.js command prompt or Terminal, execute the following commands (you may need to wrap the connections string in quotes, depending on the terminal application you are using). You may replace __MyIoTGateway__ with any name you'd like:

<pre>
  iothub-explorer [YOUR IOT HUB CONNECTION STRING] create MyIoTGateway --connection-string
</pre>

Once a device is created, the device information, including the device-specific connection string will be displayed.

<img src="/images/iothub-explorer01.png"/> 

The device-specific connection string identifies the device by name and includes a key that is only for that device. Copy the device connection string somewhere that you will be able to access it shortly.

# Conclusion &amp; Next Steps
Congratulations! You have created an Azure IoT Hub and a representation of a physical device. Next you will write the app that will collect data and send it to the Azure IoT Hubs.

[setup-azure-iot-hub]: ../setup-azure-iot-hub/
[sending-telemetry]: ../sending-telemetry/
[visualize-iot-with-powerbi]: ../visualize-iot-with-powerbi/
[deviceexplorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md