---
layout: page-fullwidth
title: "Setting Up Azure IoT Hub"
subheadline: "Node.js - Connected Weather Station"
teaser: "In this lab you will provision an Azure IoT Hub and an IoT Hub device."
show_meta: true
comments: true
header: no
breadcrumb: true
categories: [azure, iot-hubs, device-explorer, iothub-explorer]
author: doug_seven
permalink: /workshop/js/weather/setup-azure-iot-hub/
---
# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will provision a new Azure IoT Hub. Once you have the IoT Hub created, you will be able to create a new Azure IoT device (a software reference to your physical device) that you will use to send telemetry to Azure. 

# Setup an Azure IoT Hub
In a browser, navigate to the [Azure Portal at http://portal.azure.com](https://portal.azure.com){:target="_blank"} and login. Once logged in:

1. Click on the _New_ menu option in the upper-left
2. Select _Internet of Things_
3. Select _Azure IoT Hub_
4. Give it a name such as your name followed by 'IotLabs' (i.e. rickGrimesIotLabs)
5. Select the pricing tier (the free F1 tier works as well for this lab)
6. Select or create a new Resource Group
7. Select a location (choose the one closest to your physical location)

![Create a new IoT Hub](/images/New-IoT-Hub.png)
  
Once the IoT Hub is created, navigate into it and:

1. Click on the key icon at the top of the blade
2. In the next blade, click on the _iothubowner_ entry
3. Copy the _Connection string-primary key_ to your clipboard

![Copy the Connection string-primary key to your clipboard](/images/AzureIoTConnectionString.png)

# (On Windows) Use the Azure IoT Hub DeviceExplorer

Azure IoT Hub only allows connections from known devices that present proper credentials. In this lab series you will use either the _DeviceExplorer_ utility or the _iothub-explorer_ command line interface to provision a device for use in Azure IoT Hub. While Azure IoT Hub supports multiple authentication schemes, you will use pre-shared keys in this lab series.

The simplest way to provision a new device is with the _DeviceExplorer_ utility (Windows only). If you are using Windows, download and run [Device Explorer][deviceexplorer]. After running the installed, the _DeviceExplorer.exe_ can be found at __C:\Program Files (x86)\Microsoft\DeviceExplorer__. When you run the utility you need to input the _iothubowner_ connection strong (from the previous step) in the _IoT Hub Connection String_ field found in the _Configuration_ tab.

![DeviceExplorer](/images/deviceexplorer01.png)

## Create a New Azure IoT Device
If you are using the _DeviceExplorer_ simply open the _Management_ tab and click the _Create_ button. In the dialog that opens, enter the name of your device  - something like __MyIoTGateway__ works well. Then Click the _Create_ button, and click _Done_ on the confirmation dialog that opens.

![Create a device](/images/deviceexplorer02.png)

You will see your device in the _Devices_ list. Once a device is created, you can get the device-specific connection string by selecting it in the _Devices_ list, right-clicking and selecting _Copy connection string for selected device_:

![Device list](/images/deviceexplorer03.png) 

# (On Any Platform) Use the iothub-explorer CLI

{% include azure/install-iothub-explorer.md %}

## Create a New Azure IoT Device

{% include azure/new-device-iothubexplorer.md %}

# Conclusion &amp; Next Steps
Congratulations! You have created an Azure IoT Hub and a representation of a physical device. Next you will write the app that will collect data and send it to the Azure IoT Hubs.

<a class="radius button small" href="../sending-telemetry/">Go to 'Sending Telemetry to the Cloud' ›</a>

[setup-azure-iot-hub]: ../setup-azure-iot-hub/
[sending-telemetry]: ../sending-telemetry/
[visualize-iot-with-powerbi]: ../visualize-iot-with-powerbi/
[deviceexplorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md