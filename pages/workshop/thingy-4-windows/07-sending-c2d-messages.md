---
layout: "page-fullwidth"
title: "Lab 97: Sending Cloud-to-Device (C2D) Messages"
subheadline: "Building Connected Things with Windows 10 IoT and Microsoft Azure"
teaser: "In this lab you will extend your website by adding the ability to control the THingLabs Thingy&trade; remotely. The website will send messages to the Thingy via Azure."
show_meta: true
comments: true
header: no
breadcrumb: true
categories: [raspberry-pi, windows-10, grove, c#, iot, maker, azure, azure-iot-hub]
permalink: /workshop/thingy-4-windows/sending-c2d-messages/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will extend your website by adding the ability to control the ThingLabs Thingy&trade; remotely. The website will send messages to the Thingy via Azure.

# Add Newtonsoft Json Library
In a previous lab, you built the ThingLabs Thingy&trade; for Windows 10 IoT Core and deployed a website to display data from the Thingy. In this lab, you will send messages from the Microsoft Azure IoT Hub to the Thingy. In the same way that you added a NuGet package for the Azure IoT SDK, you need to add a package for working with JSON data. 

1. Open the __Thingy__ project.
2. Click on the _Project_ menu and select _Manage NuGet Packages._
3. Use the search field to search for __Newtonsoft.Json__.
4. Click on the __Install__ button to install the package.

# Create a Command Message Class
It is easiest to transition the JSON message you receive from the cloud into a class so that you can avoid string parsing.

1. Create a new class called __CommandMessage__.
2. Add a single property named __LedStatus__ with a type of __int__.

{% highlight csharp %}
public class CommandMessage
{
    public int LedState { get; set; }
}
{% endhighlight %}

# Add A Method to Receive Messages from the Cloud
Now you will add a method to the end of the __StartupTask__ class that will look for messages from the __deviceClient__ that you created in a previous lab.

1. Locate the end of the __StartupTask__ and add a method called __ReceiveCloudToDeviceMessagesAsync__.

{% highlight csharp %}
private async void ReceiveCloudToDeviceMessagesAsync()
{
    while (true)
    {
        Message receivedMessage = await deviceClient.ReceiveAsync();

        // if command messages aren't being sent null messages will be received
        // and I need to continue to run the loop looking for command messages
        if (receivedMessage == null)
        {
            continue;
        }

        var receivedJson = Encoding.UTF8.GetString(receivedMessage.GetBytes());
        var commandMessage = JsonConvert.DeserializeObject<CommandMessage>(receivedJson);

        blueLed.ChangeState((SensorStatus)commandMessage.LedState);

        await deviceClient.CompleteAsync(receivedMessage);
    }
}
{% endhighlight %}

2. Locate the end of the __Run(IBackgroundTaskInstance taskInstance)__ method and add a call to the __ReceiveCloudToDeviceMessagesAsync__ method.

{% highlight csharp %}
public void Run(IBackgroundTaskInstance taskInstance)
{
    // existing code from method omitted for brevity
    
    ReceiveCloudToDeviceMessagesAsync();
}
{% endhighlight %}

# Run the Application Again and Turn the LED On and Off
Now you can browse to the website you deployed in [lab 05](../sending-d2c-messages) and use the buttons to turn the blue LED on and off.

1. Browse to your website and enter the name of your device as the __device id__.
2. Press the __LED ON__ button and see your blue led turn on.
3. Press the __LED OFF__ button and see your blue led turn off.

![Send LED ON and OFF messages](/images/workshops/thingy-4-windows/sending-c2d-messages.png) 

# Conclusion
Congratulations! In this lab, you updated the __Thingy__ application to receive messages from a website through Azure IoT Hub. The core concepts you've learned are:

1. Using the Azure IoT SDK to connect to Azure and send cloud-to-device (C2D) messages.
2. Handling the times when the device didn't receive an messages from the cloud
