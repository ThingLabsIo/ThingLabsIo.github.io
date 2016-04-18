---
layout: "page-fullwidth"
title: "Nightlight"
subheadline: "Building Connected Things with Windows 10 IoT and Microsoft Azure"
teaser: "In this lab, you will create a device that responds to ambient light and changes the intensity of an LED - a nightlight."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [raspberry-pi, windows-10, grove, c#, iot, maker]
permalink: /workshop/thingy-4-windows/nightlight/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab, you will create a simple _Thing_ using a Windows 10 IoT device and the Universal Windows Platform. 

# Bill of Materials
What you will need:

1. [Raspberry Pi 2](http://www.amazon.com/Raspberry-Pi-Model-Project-Board/dp/B00T2U7R7I/)
2. [5V (2A to 3A) Switching Power Supply w/ MicroUSB Cable](http://www.amazon.com/CanaKit-Raspberry-Supply-Adapter-Charger/dp/B00MARDJZ4/)
3. From the [GrovePi+ Starter Kit for Raspberry Pi](http://www.seeedstudio.com/depot/GrovePi-Starter-Kit-for-Raspberry-Pi-ABB23-CE-certified-p-2572.html)
 * GrovePi shield
 * Grove LED (any color) × 1 + connector cable
 * Grove Light Sensor × 1 + connector cable
4. A Wi-Fi Adapter (choose one from the list [here](http://ms-iot.github.io/content/en-US/win10/SupportedInterfaces.htm#WiFi-Dongles))
5. 8GB micro SD card - class 10 or better. Microsoft suggests one of the following:
	* [Samsung 32GB EVO Class 10 Micro SDHC up to 48MB/s with Adapter (MB-MP32DA/AM)](http://www.amazon.com/gp/product/B00IVPU786)
	* [SanDisk Ultra Micro SDHC, 16GB Card](http://www.amazon.com/SanDisk-Ultra-Micro-SDHC-16GB/dp/9966573445).

If you haven't already done so, follow the setup instructions at ['Setting Up Your Raspberry Pi 2']({{ site.url }}/workshop/thingy-4-windows/setup-rpi2/).

# Overview
In this lab, you will combine the output device you previously used (a Grove LED module) with an input sensor - a Grove Light Sensor. You will use the measurement of ambient light from the Light Sensor to control the intensity of the LED. Basically, you are making a nightlight. 

The Grove Light Sensor is made up of a photoresistor and a 10k Ohm resistor. A _photoresistor_, also known as a _light-dependent resistor (LDR)_ or a photocell, works by limiting the amount of voltage that passes through it based on the intensity of light detected. The resistance decreases as light input increases - in other words, the more light, the more voltage passes through the photoresistor. By measuring how much voltage is passed through the photoresistor you can determine the range of ambient light, using a measurement of 0-1023. The GrovePi libraries do this for you and expose it as a method call - `ILightSensor.SensorValue()` (or even `ILightSensor.Resistance()`).

In this lab, you will measure the amount of ambient light and increase the brightness intensity of an LED at an inverse rate to the amount of light (i.e. as it gets darker the LED gets brighter). In the previous lab, you connected the LED sensor to digital pin 4, which only supports On/Off states. There is a technique for using a digital device (like an LED) to simulate analog behavior, such as fading the brightness of an LED over a range of 256 value stops (0-255) instead of simply 0 or 1 - _Pulse Width Modulation_.

# Pulse Width Modulation (PWM)
Pulse Width Modulation (PWM) is a technique for simulating analog values on a digital actuator. PWM simulates analog data by creating a square wave (basically a repeating switch between on and off) where the duration of 'on' time is the pulse width. If the square wave has a 50% pulse width (more commonly known as a duty cycle), then the output from that pin is equal amounts on and off. If the duty cycle is 25% then the output from the pin will be on for only one-quarter of the duty cycle (inversely it will be off for three times as long as it is on - 25% on, 75% off).

![PWM Duty Cycle]({{site.url}}/images/duty-cycle.jpg)

Because the time window of a cycle is too fast for the human eye to perceive (about 2 milliseconds for this example), instead of causing an LED to strobe or flicker, it simply appears to be more or less bright. Using a 25% duty cycle the LED would be __On__ for half a millisecond and __Off__ for 1.5 milliseconds, which makes the LED appear to be at 25% brightness. So while we aren't truly sending analog data to a digital LED, we are using PWM to simulate the effect of analog data.

GrovePi sockets __D3__, __D5__, __D6__ support _Pulse Width Modulation (PWM)_ which means you can write 8-bit values __0-255__ with `analogWrite()`.

# Connecting the Sensors
Connect the LED Sensor and Light Sensor to the GrovePi shield as illustrated here:

![Connect the LED and Light Sensor](/images/workshops/thingy-4-windows/nightlight.jpg)

* Connect the LED Sensor to D6
* Connect the Light Sensor to A2

# Build the App
Like the ['Hello, Windows IoT!'](../hello-windows-iot/) lab, the _Nightlight_ application is a _Background Application (IoT)_ project in Visual Studio. 

1. Launch Visual Studio and start a new __Background Application (IoT)__ (found in the _Templates -> C# -> Windows -> Windows IoT Core_ node).
2. Name the application __Nightlight__.
3. Add a reference to the GrovePi libraries the same way you did in ['Hello, Windows IoT!](../hello-windows-iot/).
4. Open the __StartupTask.cs__ file. Add the following to the __using__ statements at the top of the file. 

{% highlight csharp %}
// Add using statements to the GrovePi libraries
using GrovePi;
using GrovePi.Sensors;
using Windows.System.Threading;
{% endhighlight %}

## Define the Class-level Variables
There are two (2) class-level variables you will use to refer to the physical Light Sensor and LED Module:

* __redLed__ - an _ILed_ instance that derives from `GrovePi.Sensor` and exposes properties to get the current status of the LED and change its state. 
* __lightSensor__ - an _ILightSensor_ the also derives from `GrovePi.Sensor` and is instantiated (later) on an analog pin.

1. Add the following class-level variable definitions inside the `public sealed class StartupTask : IBackgroundTask` class definition:

{% highlight csharp %}
/**** DIGITAL SENSORS AND ACTUATORS ****/
// Connect the Red LED to digital port 6
ILed redLed;

/**** ANALOG SENSORS ****/
// Connect the light sensor to analog port 2
ILightSensor lightSensor;
{% endhighlight %}

There are five (5) class-level variables you will use in this application:

* __ambientLightThreshold__ - an _int_ constant between 0 (dark) and 1023 (bright) that defines the measurement of ambient light at which the LED should be in a completely off state.
* __actualAmbientLight__ - an _int_ variable to track the current value of ambient light as measured by the light sensor.
* __brightness__ - an _int_ variable to track the current LED brightness.
* __timer__ - a _ThreadPoolTimer_ instance that will control the rate of sensor and actuator interactions. 
* __deferral__ - a _BackgroundTaskDeferral_ instance that will allow the application to continue to run even after the `Run()` method has completed.

1. Add the following class-level variable definitions inside the `public sealed class StartupTask : IBackgroundTask` class definition:

{% highlight csharp %}
/**** Constants and Variables ****/
// Decide on a level of ambient light at which the LED should
// be in a completely off state (e.g. sensorValue == 700)
const int ambientLightThreshold = 700;
// Create a variable to track the current value from the Light Sensor
private int actualAmbientLight;
// Create a variable to track the current LED brightness
private int brightness;
// Create a timer to control the rate of sensor and actuator interactions
ThreadPoolTimer timer;
// Create a deferral object to prevent the app from terminating
BackgroundTaskDeferral deferral;
{% endhighlight %}

## Instantiate the Objects and Start the Timer
Inside the `Run(IBackgroundTaskInstance taskInstance)` method, you need to instantiate all of the objects you defined, including the timer. The time will begin _ticking_ as soon as it is created. Within the timer's _TimePeriodElapsed_ callback, you will do all of the interaction with the sensors and actuators.

1. Modify the `Run(IBackgroundTaskInstance taskInstance)` method as follows. 

{% highlight csharp %}
public void Run(IBackgroundTaskInstance taskInstance)
{
    // Get the deferral instance
    deferral = taskInstance.GetDeferral();
    
    // Instantiate the sensors and actuators
    redLed = DeviceFactory.Build.Led(Pin.DigitalPin6);
    lightSensor = DeviceFactory.Build.LightSensor(Pin.AnalogPin2);
    
    // Start a timer to check the sensor and activate the actuator five times per second
    timer = ThreadPoolTimer.CreatePeriodicTimer(Timer_Tick, TimeSpan.FromMilliseconds(200));
}
{% endhighlight %}

## Handle the TimePeriodElapsed Event
As previously stated, you will handle measuring light data and setting the LED brightness in the timer's _TimePeriodElapsed_ event by doing the following:

1. Get the light value from the light sensor by calling `lightSensor.SensorValue()`.
2. Evaluate the light value against the `ambientLightThreshold` value.
3. If the light value is below the threshold (i.e. dark enough that the LED should be illuminated), map the difference between the light value and the threshold to an 8-bit range (0-255).
4. Use the value derived above to set the intensity/brightness of the LED.

## Interact with Sensors Using the Timer
Throughout these labs, you will use a feature in Visual Studio called light bulbs. Light bulbs are a new productivity feature in Visual Studio 2015. They are icons that appear in the Visual Studio editor and that you can click to perform quick actions including refactoring fixing errors. Light bulbs bring error-fixing and refactoring assistance into a single focal point, often right on the line where you are typing. As you write the code in this lab you will add calls to methods that don't yet exist. The editor will indicate this to you by putting a red "squiggle" underline beneath the method call. When you hover over the offending code a light bulb will appear and you can expand it to see options for generating the missing method. 

Where you created the timer in the `ThreadPoolTimer.CreatePeriodicTimer(Timer_Tick, TimeSpan.FromMilliseconds(200))` call do the following:

1. Hover the mouse over the `Timer_Tick` reference until a light bulb appears.
2. Click the down arrow and select __Generate method ‘StartupTask.Timer_Tick’__.
3. Add the following code for the Timer_Tick method.

{% highlight csharp %}
private void Timer_Tick(ThreadPoolTimer timer)
{
    try
    {
        // Capture the current value from the Light Sensor
        actualAmbientLight = lightSensor.SensorValue();
        
        // If the actual light measurement is lower than the defined threshold
        // then define the LED brightness based on the delta between the actual
        // ambient light and the threshold value
        if (actualAmbientLight < ambientLightThreshold)
        {
            // Use a range mapping method to conver the difference between the
            // actual ambient light and the threshold to a value between 0 and 255
            // (the 8-bit range of the LED on D6 - a PWM pin).
            // If actual ambient light is low, the differnce between it and the threshold will be
            // high resulting in a high brightness value.
            brightness = Map(ambientLightThreshold - actualAmbientLight, 0, ambientLightThreshold, 0, 255);
        }
        else
        {
            // If the actual ambient light value is above the threshold then
            // the LED should be completely off. Set the brightness to 0
            brightness = 0;
        }
        
        // AnalogWrite uses Pulse Width Modulation (PWM) to 
        // control the brightness of the digital LED on pin D6.
        redLed.AnalogWrite(Convert.ToByte(brightness));
    }
    catch (Exception ex)
    {
        // NOTE: There are frequent exceptions of the following:
        // WinRT information: Unexpected number of bytes was transferred. Expected: '. Actual: '.
        // This appears to be caused by the rapid frequency of writes to the GPIO
        // These are being swallowed here
        
        // If you want to see the exceptions uncomment the following:
        // System.Diagnostics.Debug.WriteLine(ex.ToString());
    }
}
{% endhighlight %}

## Create a Range Mapping Method
In the preceding code, you determine the LED brightness by mapping the delta of the actual light and the threshold value to an 8-bit range. This is don using a custom method - `Map()`. Using the Visual Studio light bulb feature, add the `Map()` method.

Where you wrote `brightness = Map(ambientLightThreshold - actualAmbientLight, 0, ambientLightThreshold, 0, 255);`, do the following:

1. Hover the mouse over the `Map` reference until a light bulb appears.
2. Click the down arrow and select __Generate method ‘StartupTask.Map’__.
3. Modify the `Map()` method as follows:

{% highlight csharp %}
// This Map function is based on the Arduino Map function
// http://www.arduino.cc/en/Reference/Map
private int Map(int src, int in_min, int in_max, int out_min, int out_max)
{
    return (src - in_min) * (out_max - out_min) / (in_max - in_min) + out_min;
}
{% endhighlight %}

If you want to compare your code with the master lab code, you can find it [in the __ThingLabs - Thingy4Windows__ Github repo here](https://github.com/ThingLabsIo/Thingy4Windows/blob/master/Nightlight/Nightlight/StartupTask.cs).

# Run the App on a Device
As in the previous lab, you will build the application locally and then deploy it to the RPi2 and open a remote debugging session using Visual Studio.

1. Ensure __ARM__ is selected in the _Solution Platforms_ drop-down list in the toolbar
2. Select __Remote Machine__ from the _Device_ list in the toolbar.

![Select ARM](/images/workshops/thingy-4-windows/target_remote_machine.png)

You will be prompted with the _Remote Connections_ dialog. You can select your device in one of two ways:

1. Select your device from the list of _Auto Detected_ devices, __OR__ 
2. Type in the __device name__ or __IP address__ into the _Manual Configuration_ text box (set the _Authentication Mode_ to __Universal (Unencrypted Protocol)__) and click __Select__.

![Select your device](/images/workshops/thingy-4-windows/find_remote_machine.png)

>NOTE: You can verify or modify these values by navigating to the project properties (double-click the Properties node in Solution Explorer and click on the Debug tab on the left.

1. Now press __F5__ to run the application and you should see (in the _Output_ window) it building locally and then deploying on the RPi2.

Once the application is deployed and running, try changing the amount of light the light sensor is exposed to. As it gets darker, the LED should glow brighter (it will glow brightest in complete darkness). When there is enough light, the threshold will be surpassed and the LED will turn off completely.

# Conclusion &amp; Next Steps
Congratulations! You have created a _Thing_ that uses an input sensor to measure ambient light to control the output to an LED. The concepts you learned in this lab are:

1. Input sensors vs. output actuators.
2. Pulse Width Modulation (PWM), which is a technique for simulating analog values on a digital actuator by turning the actuator on and off for portions of the duty cycle.
3. Using the Visual Studio light bulb capability to easily generate method stubs.

In the [next lab][nextlab], you will extend this device to build the __ThingLabs Thingy&trade; for Windows IoT__ - a multi-sensor, multi-actuator device you will use for the remainder of this workshop.

<a class="radius button small" href="{{ site.url }}/workshop/thingy-4-windows/thingy/">Go to 'ThingLabs Thingy&trade;' ›</a>

[nextlab]: /workshop/thingy-4-windows/thingy/