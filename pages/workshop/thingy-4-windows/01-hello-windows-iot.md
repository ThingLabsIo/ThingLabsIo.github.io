---
layout: "page-fullwidth"
title: "Lab 01: Hello, Windows IoT!"
subheadline: "Building Connected Things with Windows 10 IoT and Microsoft Azure"
teaser: "In this lab, you will create a simple 'Thing' using Windows 10 IoT Core."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [raspberry-pi, windows-10, grove, c#, iot, maker]
permalink: /workshop/thingy-4-windows/hello-windows-iot/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab, you will create a simple _Thing_ using a Windows 10 IoT device and the Universal Windows Platform. 

# Bill of Materials
What you will need:

1. [Raspberry Pi 2](http://www.amazon.com/Raspberry-Pi-Model-Project-Board/dp/B00T2U7R7I/)
3. From the [GrovePi+ Starter Kit for Raspberry Pi](http://www.seeedstudio.com/depot/GrovePi-Starter-Kit-for-Raspberry-Pi-ABB23-CE-certified-p-2572.html)
 * GrovePi shield
 * Grove LED (any color) × 1 + connector cable
3. [5V (2A to 3A) Switching Power Supply w/ MicroUSB Cable](http://www.amazon.com/CanaKit-Raspberry-Supply-Adapter-Charger/dp/B00MARDJZ4/)
4. A Wi-Fi Adapter (choose one from the list [here](http://ms-iot.github.io/content/en-US/win10/SupportedInterfaces.htm#WiFi-Dongles))
5. 8GB micro SD card - class 10 or better. Microsoft suggests one of the following:
	* [Samsung 32GB EVO Class 10 Micro SDHC up to 48MB/s with Adapter (MB-MP32DA/AM)](http://www.amazon.com/gp/product/B00IVPU786)
	* [SanDisk Ultra Micro SDHC, 16GB Card](http://www.amazon.com/SanDisk-Ultra-Micro-SDHC-16GB/dp/9966573445).

If you haven't already done so, follow the setup instructions at ['Setting Up Your Raspberry Pi 2']({{ site.url }}/workshop/thingy-4-windows/setup-rpi2/).

# Connect the Grove Parts (Shield and LED)
The Raspberry Pi 2 connects to the physical world through the GPIO pins. GPIO stands for General Purpose Input/Output and refers to the two rows of pins on Raspberry Pi 2. The GPIO pins are a physical interface between the Raspberry Pi 2 and the physical world. Through your app, you can designate pins to either receive input or send output. The inputs can be from switches, sensors or other devices. The outputs can be LEDs, servos, motors or countless other devices. Twenty-six of the 40 pins are GPIO pins; the others are power, ground, or reserved pins.

![RPi2 Pin Map](/images/rpi2/rpi12_pinout.png)

The GrovePi Shield simplifies accessing the pins by providing connectors that you can simply plug sensors and devices into. The GrovePi Shield exposes GPIO pins (labeled D2-D8 for digital and A0-2 for analog), I2C (pronounced Eye-Squared-See) and Serial/SPI connections.

![GrovePi Shield](/images/rpi2/GrovePi-Shield.jpg)

>Note: The Raspberry Pi 2 doesn't have analog GPIO. The GrovePi shield includes an Analog-Digital Converter enabling you to connect analog devices to the A0-A2 connectors.

You can read more about how the GrovePi Shield works [here](../grovepi/).

Connect the GrovePi shield to the Raspberry Pi 2.

![Connect the GrovePi Shield](/images/rpi2/Connect-GrovePi.jpg)

Connect the LED to the D4 jack.

![Connect the LED to D4](/images/workshops/thingy-4-windows/blink_example.jpg)

# Create the Device Application 
A Universal Windows app is a Windows experience that is built upon the Universal Windows Platform (UWP), which was first introduced in Windows 8 as the Windows Runtime. The UWP enables you to write an app that targets a device family, such as IoT devices. In fact, the Universal app that you write may be able to run on multiple device families, depending on the device characteristics that it takes advantage of. In this lab, you will create a Universal app targeting IoT devices running Windows 10. In theory this could be nearly any device, such as a phone, a tablet or a Raspberry Pi 2. The Universal app you will write, however, will access the General Purpose Input/Output (GPIO) of the device, so the app won't be compatible with devices that don't have a GPIO. 

## Create a New Background Task Project

1. Launch Visual Studio and start a new __Background Application (IoT)__ (found in the _Templates -> C# -> Windows -> Windows IoT Core_ node).
2. Name the application __HelloWindowsIoT__.

![Create a new IoT Background application](/images/workshops/thingy-4-windows/new_hello.png)

## Add a Reference to the GrovePi Libraries
The GrovePi libraries provide an abstraction over the Windows 10 IoT Core GPIO, I2C and SPI interfaces expose the sensor and device functionality as objects. The GrovePi libraries are easy to install using the Package Manager Console.

1. To install GrovePi for Windows IoT, run the following command from the __Package Manager Console__

```
Install-Package GrovePi
```
2. Verify that GrovePi v1.0.7 was installed by reading the log in the Package Manager Console.

## Define the Libraries the App Requires
This application will run as a background task on a Windows 10 IoT device. Unlike a graphical (or _headed_) UWP application, where the Windows IoT OS is limited to running only one app at a time, Windows 10 IoT Core (and better) can run multiple BackGround Task applications at once. These are _headless_ applications. That is to say, there is no UI. You will write code that performs the necessary functions for the application, but there will not be any UI. This example will demonstrate how to blink and LED on and off.

1. Open the __StartupTask.cs__ file. Add the following to the __using__ statements at the top of the file. 

{% highlight csharp %}
// Add using statements to the GrovePi libraries
using GrovePi;
using GrovePi.Sensors;
using Windows.System.Threading;
{% endhighlight %}

## Define the Class-level Variables
There are three class-level variables you will use in this application:

* `timer` - A _ThreadPoolTimer_ instance that will control the LED blinking interval. 
* `deferral` - A _BackgroundTaskDeferral_ instance that will allow the application to continue to run even after the ```Run()``` method has completed.
* `led` - A _GrovePi.ILed_ instance that represents the Grove LED sensor.

1. Add the following class-level variable definitions inside the ```public sealed class StartupTask : IBackgroundTask``` class definition:

{% highlight csharp %}
namespace HelloWindowsIoT
{
    public sealed class StartupTask : IBackgroundTask
    {
        ThreadPoolTimer timer;
        BackgroundTaskDeferral deferral;
        ILed led;

        public void Run(IBackgroundTaskInstance taskInstance)
        {
            
        }
    }
}
{% endhighlight %}

## Instantiate the LED Sensor and a Timer to Control It
For this application, you will use a _ThreadPoolTimer_ to raise an event at a predefined interval. (In this example you will raise events once per second.) 

1. Inside the `public void Run(IBackgroundTaskInstance taskInstance)` function create a new object instance using the `led` variable, and  a _ThreadPoolTimer_ that will raise an event every one-second.

{% highlight csharp %}
public void Run(IBackgroundTaskInstance taskInstance)
{
    deferral = taskInstance.GetDeferral();

    // Connect the LED to digital port 4
    led = DeviceFactory.Build.Led(Pin.DigitalPin4);

    // Create a timer that will 'tick' every one-second
    timer = ThreadPoolTimer.CreatePeriodicTimer(this.Timer_Tick, TimeSpan.FromSeconds(1));
}
{% endhighlight %}

## Handle the Timer_Tick Event
In the previous section, you defined an event handler that will be invoked each time the timer ticks off using the ```ThreadPoolTimer.CreatePeriodicTimer(callback, TimeSpan))``` method. Now you will add the callback event (aka the event handler). When the Timer\_Tick event handler is invoked it will check the current state of the LED (on or off) and switch it to the opposite state.

1. Using the Visual Studio refactoring tools, you can generate the method stub for the __Timer\_Tick__ event handler. Hover over the _Timer\_Tick_ text until a light bulb appears. Click the down arrow and select _Generate method 'StartupTask.Timer\_Tick'_ 

![Generate Timer\_Tick Event Handler](/images/workshops/thingy-4-windows/timer_tick.png)

2. Add the following code for the _Timer\_Tick_ event handler.

{% highlight csharp %}
private void Timer_Tick(ThreadPoolTimer timer)
{
    try
    {
        led.ChangeState( (led.CurrentState == SensorStatus.Off) ? SensorStatus.On : SensorStatus.Off );
    }
    catch (Exception ex)
    {
        // Swallow the exception
    }
}
{% endhighlight %}

If you want to compare your code with the master lab code, you can find it in the __ThingLabs - Thingy4Windows__ Github repo [here](https://github.com/ThingLabsIo/Thingy4Windows/blob/master/HelloWindowsIoT/HelloWindowsIoT/StartupTask.cs).

# Run the App on a Raspberry Pi 2
To run the application you will build it locally and then deploy it to the Raspberry Pi 2 and open a remote debugging session. Fortunately, this is all encapsulated in a simple gesture once you have it configured.

1. Ensure __ARM__ is selected in the _Solution Platforms_ drop-down list in the toolbar
2. Select __Remote Machine__ from the _Device_ list in the toolbar.

![Select ARM](/images/workshops/thingy-4-windows/target_remote_machine.png)

3. You will be prompted with the _Remote Connections_ dialog. You can select your device in one of two ways:

* Select your device from the list of _Auto Detected_ devices, __OR__ 
* Type in the __device name__ or __IP address__ into the _Manual Configuration_ text box (set the _Authentication Mode_ to __Universal (Unencrypted Protocol)__) and click __Select__.

![Select your device](/images/workshops/thingy-4-windows/find_remote_machine.png)

4. Now press __F5__ to run the application and you should see (in the _Output_ windows) it building locally and then deploying on the Raspberry Pi 2. You will see the LED blink once per second.

>NOTE: You can verify or modify these values by navigating to the project properties. This can be accomplished by double-clicking the Properties node in Solution Explorer and clicking on the Debug tab on the left.

# Conclusion &amp; Next Steps
Congratulations! You have built a Windows 10 IoT application that controlled a device connected to a Raspberry Pi 2. The core concepts you've learned are:

1. Building a Windows 10 IoT Background Task application that can run on a Windows 10 IoT device. 
2. Using _BackgroundTaskDeferral_ to enable an application to run even after the ```Run()``` method completes.
3. Controlling the state of a device. 

In the [next lab][nextlab], you will build a more complicated _Thing_ that uses both input sensors and output devices. 

<a class="radius button small" href="{{ site.url }}/workshop/thingy-4-windows/nightlight/">Go to 'Lab 02: Nightlight' ›</a>

[nextlab]: /workshop/thingy-4-windows/nightlight/