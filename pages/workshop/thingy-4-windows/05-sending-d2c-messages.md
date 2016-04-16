---
layout: "page-fullwidth"
title: "Sending Device-to-Cloud (D2C) Messages"
subheadline: "Building Connected Things with Windows 10 IoT and Microsoft Azure"
teaser: "In this lab you will build send messages from the Thingy you have built to the cloud."
show_meta: true
comments: true
header: no
breadcrumb: true
categories: [raspberry-pi, windows-10, grove, c#, iot, maker, azure, azure-iot-hub]
permalink: /workshop/thingy-4-windows/sending-d2c-messages/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

# Bill of Materials
What you will need:

1. The ThingLabs Thingy&trade; created in the ['ThingLabs Thingy' lab](../thingy/).
2. The Visual Studio Project for the Thingy created in the ['ThingLabs Thingy' lab](../thingy/).


# Add the Azure ioT SDK to Your Project
In a previous lab you built the ThingLabs Thingy&trade; for Windows 10 IoT Core. Now its time to add the __I__ to your __IoT__ solution. In this lab you will send messages from the Thingy to the Microsoft Azure IoT Hub you created in the previous lab. There is an Azure IoT SDK for C# that enables all of the connection and communications with your Azure IoT Hub.  The SDK is available as a NuGet package that you can easily add to your project. 

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

<a class="radius button small" href="{{ site.url }}/workshop/thingy-4-windows/storing-displaying-data/">Go to 'Storing and Displaying IoT Data' ›</a>

[nextlab]: ../storing-displaying-data/