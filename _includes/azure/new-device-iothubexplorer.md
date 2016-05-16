If you are on a non-Windows machine, or prefer to use a command line interface instead of the _DeviceExplorer_ utility, you can provision a new Azure IoT Hub device using the _iothub-explorer_ command line interface.

In the same directory as before, using the Node.js command prompt or Terminal, execute the following command (you may need to wrap the connections string in quotes, depending on the command line application you are using).

<pre>
  iothub-explorer [YOUR IOT HUB CONNECTION STRING] create Thingy --connection-string
</pre>

Once a device is created, the device information, including the device-specific connection string will be displayed.

![New Device using IoT Hub Explorer]({{ site.url }}/images/iothub-explorer01.png)

The device-specific connection string identifies the device by name and includes a key that is only for that device. Copy the device connection string somewhere that you will be able to access it shortly.