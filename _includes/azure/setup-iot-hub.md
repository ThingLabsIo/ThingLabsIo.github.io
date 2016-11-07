In a browser, navigate to the Azure Portal at [https://portal.azure.com](https://portal.azure.com).

1. Login to the account you created in [Getting Started](../getting-started/).
2. Click on the __New__ menu option in the upper-left.
3. Select __Internet of Things__.
4. Select __Azure IoT Hub__.
5. Give it a unique name (e.g. _rickgrimes-iot-hub_).
6. Select or create a new __Resource Group__.
7. Select a location. (Choose the one closest to your physical location.)

![New IoT Hub]({{site.url}}/images/rpi2/rpi2_New-IoT-Hub.png)
  
Once the IoT Hub is created, navigate into it and:

1. Click on the _key_ icon at the top of the blade.
2. In the next blade, click on the __iothubowner__ entry.
3. Copy the __Connection string-primary key__ to your clipboard.

![Azure IoT Hub Connection String]({{site.url}}/images/rpi2/rpi2_AzureIoTConnectionString.png)

Azure IoT Hub only allows connections from known devices that present proper credentials. In this lab you will use either the _DeviceExplorer_ utility or the _iothub-explorer_ command line interface to provision a device for use in Azure IoT Hub. Although Azure IoT Hub supports multiple authentication schemes, you will use pre-shared keys in this lab.