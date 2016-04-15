---
layout: "page-fullwidth"
title: "Sending Device-to-Cloud (D2C) Messages"
subheadline: "Building Connected Things with Windows 10 IoT and Microsoft Azure"
teaser: "In this lab you will build a Universal Windows Platform application that collects ambient light data and sends it to the Cloud."
show_meta: true
comments: true
header: no
breadcrumb: true
categories: [raspberry-pi, windows-10, grove, c#, iot, maker, azure, azure-iot-hub]
permalink: /workshop/full-day-windows/sending-d2c-messages/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will build a Universal Windows Platform application that detects ambient light and sends the data that is being collected to Azure IoT Hub. In following lab you will build a data pipeline to process the incoming data stream and output it to a visualization tool.

# Bill of Materials
What you will need:

1. The ThingLabs Thingy&trade; created in the ['ThingLabs Thingy' lab](../thingy/)

# Build the Application
In this application you will read the voltage value coming into the ADC from the voltage divider - the higher the voltage, the darker it is (remember, you are reading in the residual voltage, which is diverted to the ADC when there is resistance in the photoresistor). You will use the _darkness_ value to determine if the LED should be on or off.

The ADC is connected to the RPi2 through the Serial Peripheral Interface (SPI) bus. SPI is a synchronous serial communication interface specification used for short distance communication, primarily in embedded systems. SPI devices communicate in full duplex mode using a master-slave architecture with a single master. The master device originates the frame for reading and writing. Multiple slave devices are supported through selection with individual slave select (SS) lines. SPI is a four-wire serial bus as follows:

1. SCLK - Serial Clock (output from master).
2. MOSI - Master Output, Slave Input (output from master).
3. MISO - Master Input, Slave Output (output from slave).
4. SS - Slave Select (active low, output from master).

We won't go any deeper into SPI or the pin layout of the two ADCs - suffice to say that the ADC is wired up to support the four-channel SPI bus, plus supply voltage and ground. The wire connecting the voltage divider to the ADC is the input channel you will read the residual voltage from. 

## Modify the ThingLabs Thingy&trade;

//TODO

## Send A Message to Azure IoT Hub

//TODO

{% highlight csharp %}
// Instantiate the Azure device client
deviceClient = DeviceClient.CreateFromConnectionString(IOT_HUB_CONN_STRING);
{% endhighlight %}

Next, replace the comment<code>// TODO: Send messages to Azure IoT Hub every one-second</code> with:

{% highlight csharp %}
// Send messages to Azure IoT Hub every one-second
sendMessageTimer = new Timer(this.MessageTimer_Tick, null, 0, 1000);
{% endhighlight %}

Use the Visual Studio light bulb feature to create the __MessageTimer\_Tick()__ event handler.

{% highlight csharp %}
private void MessageTimer_Tick(object state)
{
    SendMessageToIoTHubAsync(adcValue);
}
{% endhighlight %}

Each time the _MessageTimer_ ticks (once per second) this event handler will invoke the _SendMessageToIoTHubAsync()_ method. Use the Visual Studio light bulb feature to create the __SendMessageToIoTHubAsync()__ method. Modify the method signature to mark it as an async method.

{% highlight csharp %}
private async Task SendMessageToIoTHubAsync(int darkness)
{
    try
    {
        var payload = "{\"deviceId\": \"" +
            IOT_HUB_DEVICE +
            "\", \"location\": \"" +
            IOT_HUB_DEVICE_LOCATION +
            "\", \"messurementValue\": " +
            darkness +
            ", \"messurementType\": \"darkness\", \"localTimestamp\": \"" +
            DateTime.Now.ToLocalTime().ToString() +
            "\"}";

        // UI updates must be invoked on the UI thread
        var task = this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            MessageLog.Text = "Sending message: " + payload + "\n" + MessageLog.Text;
        });

        var msg = new Message(Encoding.UTF8.GetBytes(payload));

        await deviceClient.SendEventAsync(msg);
    }
    catch (Exception ex)
    {
        // UI updates must be invoked on the UI thread
        var task = this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            MessageLog.Text = "Sending message: " + ex.Message + "\n" + MessageLog.Text;
        });
    }
}
{% endhighlight %}

With this method you attempt to construct a JSON message payload, display it on the screen and send it to your Azure IoT Hub. The communication with the Azure IoT Hub is managed by the <code>deviceClient</code> object from the _Microsoft.Azure.Devices.Client_ namespace.

The final _MainPage.xaml.cs_ can be found [here](https://github.com/ThingLabsIo/IoTLabs/blob/master/RPi2/IoTLightSensor/IoTLightSensor/MainPage.xaml.cs) and the complete solution can be found [here](https://github.com/ThingLabsIo/IoTLabs/tree/master/RPi2/IoTLightSensor).

## Run the Application
Now you can run the application on your RPi2 and not only will you see the indicator bar changing, but you will also see the log of messages being sent to Azure IoT Hub at a rate of once per second.

# Conclusion &amp; Next Steps
Congratulations! You have built a Universal Windows Platform application that captures data from the physical word and sends it to Azure IoT Hub. The core concepts you've learned are:

1. Working with analog data using a voltage divider and an analog-t-digital converter (ADC). 
2. Configuring and using the Serial Peripheral Interface (SPI).
3. Creating and sending messages to Azure IoT Hub. 

At this point, nothing interesting is happening with that data you are sending to Azure. It is simply being persisted for a default amount of time (1-day) and then being dropped. In the [next lab][nextlab] you will create a web application to visualize the data.

<a class="radius button small" href="{{ site.url }}/workshop/full-day-windows/storing-displaying-data/">Go to 'Storing and Displaying IoT Data' ›</a>

[nextlab]: ../storing-displaying-data/