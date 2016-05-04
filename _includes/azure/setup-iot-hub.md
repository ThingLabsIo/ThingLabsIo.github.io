In a browser, navigate to the Azure Portal at [https://portal.azure.com](https://portal.azure.com). Login to the account you created in the ['Getting Started' lab](../getting-started/). Once logged in:

1. Click on the __New__ menu option in the upper-left
2. Select __Internet of Things__
3. Select __Azure IoT Hub__
4. Give it a unique name (e.g. _rickgrimes-iot-hub_).
5. Select or create a new __Resource Group__
6. Select a location (choose the one closest to your physical location)

![New IoT Hub]({{site.url}}/images/rpi2/rpi2_New-IoT-Hub.png)
  
Once the IoT Hub is created, navigate into it and:

1. Click on the _key_ icon at the top of the blade
2. In the next blade, click on the __iothubowner__ entry
3. Copy the __Connection string-primary key__ to your clipboard

![Azure IoT Hub Connection String]({{site.url}}/images/rpi2/rpi2_AzureIoTConnectionString.png)