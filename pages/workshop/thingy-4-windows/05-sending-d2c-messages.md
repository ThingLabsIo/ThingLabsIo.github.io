---
layout: "page-fullwidth"
title: "Sending Device-to-Cloud (D2C) Messages"
subheadline: "Building Connected Things with Windows 10 IoT and Microsoft Azure"
teaser: "In this lab, you will build send messages from the Thingy you have built to the cloud."
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
In a previous lab, you built the ThingLabs Thingy&trade; for Windows 10 IoT Core. Now it's time to add the __I__ to your __IoT__ solution. In this lab, you will send messages from the Thingy to the Microsoft Azure IoT Hub you created in the previous lab. There is an Azure IoT SDK for C# that enables all of the connection and communications with your Azure IoT Hub.  The SDK is available as a NuGet package that you can easily add to your project. 

1. Open the __Thingy__ project.
2. Click on the _Project_ menu and select _Manage NuGet Packages._
3. Use the search field to search for __Microsoft.Azure.Devices.Client__.
4. Click on the __Install__ button to install the package.

![IoTNightlight Project](/images/rpi2/azure-devices-client.png)

# Define the Azure IoT SDK Components
To use the Azure IoT SDK you will modify the StartupTask.cs file in the _Thingy_ project. You will add the definitions for the SDK components, such as `DeviceClient` (the client-side agent), the `Message` and the connection string for your specific device.
 
1. Open the _StartupTask.cs_ file. 
2. Add the following to the `using` statements where you have added the other `using` statements (e.g. `using GrovePi;`).

{% highlight csharp %}
using System.Threading.Tasks;
using Microsoft.Azure.Devices.Client;
{% endhighlight %}

Next, define a variable to represent the `DeviceClient`.

1. Locate the section in the _StartupTask.cs_ file indicated with the comment `/**** Constants and Variables ****/`
2. Add the following variable definition:

{% highlight csharp %}
// Define the Azure IoT SDK DeviceClient instance
private DeviceClient deviceClient;
// Create a timer to control the rate of sending messages to Azure.
private ThreadPoolTimer messageTimer;
{% endhighlight %}

The last step of the [previous lab](../setup-azure-iot-hub/) was to copy the device specific connection string for that device (although I am guessing that your copy buffer has been rewritten since then). You can get the device-specific connection string by selecting it in the DeviceExplorer __Devices__ list - right-click and select __Copy connection string for selected device__:

![Get the device-specific connection string](/images/rpi2/rpi2_deviceexplorer03.png) 

1. In the same section of the _StartupTask.cs_ file (indicated with the comment `/**** Constants and Variables ****/`) and add the code below, making changes as indicated here:
2. Use the device-specific connection string as the value of `IOT_HUB_CONN_STRING`
3. Use the name of the device you created in Azure IoT Hub as the `IOT_HUB_DEVICE`
4. Use any string value you'd like as the `IOT_HUB_DEVICE_LOCATION` (e.g. 'Home Office');

{% highlight csharp %}
// Use the device specific connection string here
private const string IOT_HUB_CONN_STRING = "YOUR DEVICE SPECIFIC CONNECTION STRING GOES HERE";
// Use the name of your Azure IoT device here - this should be the same as the name in the connections string
private const string IOT_HUB_DEVICE = "YOUR DEVICE NAME GOES HERE";
// Provide a short description of the location of the device, such as 'Home Office' or 'Garage'
private const string IOT_HUB_DEVICE_LOCATION = "YOUR DEVICE LOCATION GOES HERE";
{% endhighlight %}    

# Send Ambient Light Measurements Once per Second
The Thingy enables you to collect multiple sensor measurements (ambient light, sound, and LED state which is controlled by a button press).
 
1. In the `Run(IBackgroundTaskInstance taskInstance)` locate the code that creates the `deferral` instance (it should be the first line of code in that method).
2. Immediately after the creation of the `deferral` object, add the following (the `deferral` code is added here for reference):

{% highlight csharp %}
// Get the deferral instance
deferral = taskInstance.GetDeferral();

// Instantiate the Azure device client
deviceClient = DeviceClient.CreateFromConnectionString(IOT_HUB_CONN_STRING);
{% endhighlight %}

1. Still in the `Run(IBackgroundTaskInstance taskInstance)` method, add the following code after the `timer = ThreadPoolTimer.CreatePeriodicTimer(Timer_Tick, TimeSpan.FromMilliseconds(200));` code.

{% highlight csharp %}
// Send messages to Azure IoT Hub every one-second
// Start a timer to send messages to Azure once per second
messageTimer = ThreadPoolTimer.CreatePeriodicTimer(MessageTimer_Tick, TimeSpan.FromSeconds(1));
{% endhighlight %}

> Throughout this lab you will use a feature in Visual Studio called light bulbs. Light bulbs are a new productivity feature in Visual Studio 2015. They are icons that appear in the Visual Studio editor and that you can click to perform quick actions including refactoring fixing errors. Light bulbs bring error-fixing and refactoring assistance into a single focal point, often right on the line where you are typing. As you write the code in this lab you will add calls to methods that don't yet exist. The editor will indicate this to you by putting a red "squiggle" underline beneath the method call. When you hover over the offending code a light bulb will appear and you can expand it to see options for generating the missing method. 

Use the Visual Studio light bulb feature to create the __MessageTimer\_Tick()__ event handler.

{% highlight csharp %}
private void MessageTimer_Tick(ThreadPoolTimer timer)
{
    SendMessageToIoTHubAsync("ambientLight", actualAmbientLight);
}
{% endhighlight %}

Each time the _MessageTimer_ ticks (once per second) this event handler will invoke the _SendMessageToIoTHubAsync()_ method. Use the Visual Studio light bulb feature to create the __SendMessageToIoTHubAsync()__ method. Modify the method signature to mark it as an async method.

{% highlight csharp %}
private async Task SendMessageToIoTHubAsync(string sensorType, int sensorState)
{
    try
    {
        var payload = "{" +
            "\"deviceId\":\"" + IOT_HUB_DEVICE + "\", " +
            "\"location\":\"" + IOT_HUB_DEVICE_LOCATION + "\", " +
            "\"sensorType\":\"" + sensorType + "\", " +
            "\"sensorState\":" + sensorState + ", " +
            "\"localTimestamp\":\"" + DateTime.Now.ToLocalTime() + "\"" +
            "}";

        var msg = new Message(Encoding.UTF8.GetBytes(payload));
        
        System.Diagnostics.Debug.WriteLine("\t{0}> Sending message: [{1}]", DateTime.Now.ToLocalTime(), payload);

        await deviceClient.SendEventAsync(msg);
    }
    catch (Exception ex)
    {
        System.Diagnostics.Debug.WriteLine("!!!! " + ex.Message);
    }
}
{% endhighlight %}

With this method, once per second you construct a JSON message payload for the ambient light measurement and send it to your Azure IoT Hub. The communication with the Azure IoT Hub is managed by the `deviceClient` object from the __Microsoft.Azure.Devices.Client__ namespace.

# Run the Application
Now you can run the application on your RPi2 and you will see the log of messages being sent to Azure IoT Hub at a rate of once per second.

# Sending Messages Based on Loud Noises
For the ambient light measurement, you are sending a message once per second regardless of whether the light has changed. For sound, you can send a message only when there is a significant volume event. 

1. Locate the `Timer_Tick` method (the one from the previous lab that is used to collect sensor data). 
2. Define a state variable before the `Timer_Tick` method to maintain the state of messages sent.
2. Modify the `Timer_Tick` method as follows:

{% highlight csharp %}
// To avoid sending messages every 200ms when there is a loud sound, 
// create a boolean variable to remember if the current message has been sent
Boolean soundLevelMessageSent = false;

private void Timer_Tick(ThreadPoolTimer timer)
{
    try {
        // Capture the current ambient noise level
        soundLevel = soundSensor.SensorValue();
        
        // If the sound level exceeds a significant volume, send a message
        // Pick a sound level that would be significant based on your Thingy
        if(soundLevel > 800)
        {
            System.Diagnostics.Debug.WriteLine.WriteLine("!!!! Sound Threshold Exceeded");
            
            if(!soundLevelMessageSent)
            {
                SendMessageToIoTHubAsync("soundLevel", soundLevel);
                soundLevelMessageSent = true;
            }
        }
        else 
        {
            soundLevelMessageSent = false;
        }
        
        // The rest of the method doesn't change and is omitted here       
{% endhighlight %}

# Run the Application and Get Noisy
Run your application again. Once you see the ambient light messages being sent, make enough noise to trigger a sound-level message. You should see that message go by as well.

# Send Messages Based on Button Presses
For the ambient light measurement, you are sending a message once per second regardless of whether the light has changed. For the button, send a message based on an explicit state change (i.e. a button press event or release event). 

1. Locate the `Timer_Tick` method (the one from the previous lab that is used to collect sensor data). 
2. Define a state variable before the `Timer_Tick` method to maintain the state of messages sent.
2. Modify the `Timer_Tick` method as follows:

{% highlight csharp %}
private void Timer_Tick(ThreadPoolTimer timer)
{
    try {
        // ... code omitted for simplicity of this example
           
        // Check the button state
        if (button.CurrentState != buttonState)
        {
            // Capture the button state
            buttonState = button.CurrentState;
            // Change the state of the blue LED
            blueLed.ChangeState(buttonState);
            buzzer.ChangeState(buttonState);
            
            // Send a message to Azure indicating the state change
            SendMessageToIoTHubAsync("led", (int)buttonState);
        }
        
        // ... the rest of the method doesn't change and is omitted here       
{% endhighlight %}

# Run the Application and Get Pushy
Run your application again. Once you see the ambient light messages being sent, push the button a few times. You should see LED state message go by as well (and hear an annoying sound from the buzzer).

# Conclusion &amp; Next Steps
Congratulations! In this lab, you updated the __Thingy__ application to send messages to Azure IoT Hub. The core concepts you've learned are:

1. Using the Azure IoT SDK to connect to Azure and send device-to-cloud (D2C) messages.
2. Sending messages as a continuous stream (e.g. once per second from a continuously measuring sensor).
3. Sending messages based on events (e.g. exceeding measurement thresholds or explicit events like a button push).

At this point, nothing interesting is happening in the cloud with that data you are sending to Azure. It is simply being persisted for a default amount of time (1-day) and then being dropped. In the [next lab][nextlab], you will create a web application to visualize the data.

<a class="radius button small" href="{{ site.url }}/workshop/thingy-4-windows/storing-displaying-data/">Go to 'Storing and Displaying IoT Data' ›</a>

[nextlab]: ../storing-displaying-data/