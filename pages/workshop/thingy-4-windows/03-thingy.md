---
layout: "page-fullwidth"
title: "ThingLabs Thingy&trade;"
subheadline: "Building Connected Things with Windows 10 IoT and Microsoft Azure"
teaser: "In this lab you will create a device that responds to ambient light and changes the intensity of an LED - a nightlight."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [raspberry-pi, windows-10, grove, c#, iot, maker]
permalink: /workshop/thingy-4-windows/thingy/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will expand on the Nightlight you created in the previous lab and build the __ThingLabs Thingy&trade;__. This device will do the following:

* Capture the amount of ambient light
* Increase the brightness intensity of an LED inversely to the amount of ambient light measured.
* Capture the amount of ambient  sound
* Display the ambient light and sound measurments on an LCD display.
* The display will adjust its backlight brightness according to the amount of ambient light in the area.
* Enable a button to turn on and off an LED and trigger sound.

In the following labs you will use the ThingLabs Thingy&trade; to connect to Azure IoT services and track all of this data.

# Bill of Materials
What you will need:

1. [Raspberry Pi 2](http://www.amazon.com/Raspberry-Pi-Model-Project-Board/dp/B00T2U7R7I/)
2. [5V (2A to 3A) Switching Power Supply w/ MicroUSB Cable](http://www.amazon.com/CanaKit-Raspberry-Supply-Adapter-Charger/dp/B00MARDJZ4/)
3. From the [GrovePi+ Starter Kit for Raspberry Pi](http://www.seeedstudio.com/depot/GrovePi-Starter-Kit-for-Raspberry-Pi-ABB23-CE-certified-p-2572.html)
 * GrovePi shield
 * Grove LED (red) × 1 + connector cable
 * Grove LED (blue) × 1 + connector cable
 * Grove Light Sensor × 1 + connector cable
 * Grove Button Sensor x 1 + connector cable
 * Grove Sound Sensor x 1 + connector cable
 * Grove Buzzer x 1 + connector cable
 * Grove RGB LCD Display x 1 + connector cable
4. A Wi-Fi Adapter (choose one from the list [here](http://ms-iot.github.io/content/en-US/win10/SupportedInterfaces.htm#WiFi-Dongles))
5. 8GB micro SD card - class 10 or better. Microsoft suggests one of the following:
	* [Samsung 32GB EVO Class 10 Micro SDHC up to 48MB/s with Adapter (MB-MP32DA/AM)](http://www.amazon.com/gp/product/B00IVPU786)
	* [SanDisk Ultra Micro SDHC, 16GB Card](http://www.amazon.com/SanDisk-Ultra-Micro-SDHC-16GB/dp/9966573445).

If you haven't already done so, complete the previous lab - ['Nightlight']({{ site.url }}/workshop/thingy-4-windows/nightlight/).

# Overview
In this lab you will combine the Nightlight device you previously created (a Grove LED module and a Grove Light Sensor) with several other input sensors and output actuators. The device will:

* Capture the amount of ambient light
* Increase the brightness intensity of an LED inversely to the amount of ambient light measured.
* Display the ambient light measurment on an LCD display.
* The display will adjust its backlight brightness according to the amount of ambient light in the area.
* Enable a button to turn on and off an LED and trigger sound.

# Connecting the Sensors
Connect the LED Sensor and Light Sensor to the GrovePi shield as illustrated here:

![Connect the Sensors](/images/workshops/thingy-4-windows/thingy.jpg)

* Connect the __red__ LED module to D6
* Connect the __blue__ LED module to D5
* Connect the Button module to D4
* Connect the Light Sensor to A2
* Connect the RGB LCD Display to one of the I2C ports.

# Build the App
Like the ['Hello, Windows IoT!'](../hello-windows-iot/) and ['Nightlight'](../nightlight/) labs, the _ThingLabs Thingy&trade;_ application is a _Background Application (IoT)_ project in Visual Studio. 

1. Launch Visual Studio and start a new __Background Application (IoT)__ (found in the _Templates -> C# -> Windows -> Windows IoT Core_ node).
2. Name the application __Thingy__.
3. Add a reference to the GrovePi libraries the same way you did in the previous labs.
   * To install GrovePi for Windows IoT, run the following command from the __Package Manager Console__

    ```
    Install-Package GrovePi
    ```
4. Open the __StartupTask.cs__ file. Add the following to the __using__ statements at the top of the file. 

{% highlight csharp %}
// Add using statements to the GrovePi libraries
using GrovePi;
using GrovePi.Sensors;
using GrovePi.I2CDevices;
using Windows.System.Threading;
{% endhighlight %}

## Define the Sensors for the Thingy
There are five (5) class-level variables you will use to refer to the physical sensors and actuators:

* Digital Sensors and Actuators - sensors and actuators that have on/off (i.e. 0 or 1) states.
  * __button__ - an _IButton_ instance that derives from `GrovePi.Sensor` and exposes properties to get the current status of the button and change its state. 
  * __redLed__ - an _ILed_ instance that derives from `GrovePi.Sensor` and exposes properties to get the current status of the red LED and change its state.
  * __blueLed__ - an _ILed_ instance that derives from `GrovePi.Sensor` and exposes properties to get the current status of the blue LED and change its state.
* Analog Sensors - sensors that expose a range of measurement from 0-1023.
  * __lightSensor__ - an _ILightSensor_ instance that enables measuring ambient light and is connected on an analog pin.
* Inter-Integrated Circuit (I2C - pronounces Eye-Squared-See) - a multi-master, multi-slave, single-ended, serial computer bus
  * __display__ an _IRgbLcdDisplay__ instance that enables displaying text and background color on a screen.

You will define these as class-level variables to be used throughout the application. 

1. Add the following class-level variable definitions inside the `public sealed class StartupTask : IBackgroundTask` class definition:

{% highlight csharp %}
/**** DIGITAL SENSORS AND ACTUATORS ****/
// Connect the button sensor to digital port 4
IButtonSensor button;
// Connect the Blue LED to digital port 5
ILed blueLed;
// Connect the Red LED to digital port 6
ILed redLed;

/**** ANALOG SENSORS ****/
// Connect the light sensor to analog port 2
ILightSensor lightSensor;

/**** I2C Deices ****/
// Connect the RGB display to one of the I2C ports
IRgbLcdDisplay display;
{% endhighlight %}

## Define the Class-level Variables
There are six (6) class-level variables you will use in this application:

* __ambientLightThreshold__ - an _int_ constant between 0 (dark) and 1023 (bright) that defines the measurement of ambient light at which the LED should be in a completely off state.
* __brightness__ - an _int_ variable to track the current LED brightness.
* __actualAmbientLight__ - an _int_ variable to track the current value of ambient light as measured by the light sensor.
* __buttonState__ - a _SensorStatus_ variable to track the state of the button.
* __timer__ - a _ThreadPoolTimer_ instance that will control the rate of sensor and actuator interactions. 
* __deferral__ - a _BackgroundTaskDeferral_ instance that will allow the application to continue to run even after the `Run()` method has completed.

These are class-level variables that will be used primarily to track state of the 
1. Add the following class-level variable definitions inside the `public sealed class StartupTask : IBackgroundTask` class definition:

{% highlight csharp %}
/**** Constants and Variables ****/
// Decide an a level of ambient light at which the LED should
// be in a completely off state (e.g. sensorValue == 700)
const int ambientLightThreshold = 700;
// Create a variable to track the current red LED brightness
private int brightness;
// Create a variable to track the current value from the Light Sensor
private int actualAmbientLight;
// Create a variable to track the state of the button
private SensorStatus buttonState;
// Create a timer to control the rateof sensor and actuator interactions
private ThreadPoolTimer timer;
// Create a deferral object to prevent the app from terminating
private BackgroundTaskDeferral deferral;
{% endhighlight %}

# Code for Thingy

{% highlight csharp %}
public void Run(IBackgroundTaskInstance taskInstance)
{
    // Get the deferral instance
    deferral = taskInstance.GetDeferral();
    
    // Instantiate the sensors and actuators
    button = DeviceFactory.Build.ButtonSensor(Pin.DigitalPin4);
    blueLed = DeviceFactory.Build.Led(Pin.DigitalPin5);
    redLed = DeviceFactory.Build.Led(Pin.DigitalPin6);
    lightSensor = DeviceFactory.Build.LightSensor(Pin.AnalogPin2);
    display = DeviceFactory.Build.RgbLcdDisplay();
    
    buttonState = SensorStatus.Off;
    
    // The IO to the GrovePi sensors and actuators can generate a lot
    // of exceptions - wrap all GrovePi API calls in try/cath statements.
    try
    {
        // Set the RGB backlight to red and display a message
        display.SetBacklightRgb(255, 0, 0);
        display.SetText("The Thingy is getting started");
    }
    catch (Exception ex)
    {
        // On Error, Resume Next :)
    }
    
    // Start a timer to check the sensors and activate the actuators five times per second
    timer = ThreadPoolTimer.CreatePeriodicTimer(Timer_Tick, TimeSpan.FromMilliseconds(200));
}

private void Timer_Tick(ThreadPoolTimer timer)
{
    try
    {
        // Check the button state
        if (button.CurrentState != buttonState)
        {
            // Capture the button state
            buttonState = button.CurrentState;
            // Change the state of the blue LED
            blueLed.ChangeState(buttonState);
        }
        
        // Capture the current value from the Light Sensor
        actualAmbientLight = lightSensor.SensorValue();
        
        // Log the amount of resistance the light sensor is providing.
        System.Diagnostics.Debug.WriteLine("R: " + lightSensor.Resistance());

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
        
        // Use the brightness value to control the brightness of the RGB LCD backlight
        byte rgbVal = Convert.ToByte(brightness);
        display.SetBacklightRgb(rgbVal, rgbVal, 255);
        
        // Updae the RGB LCD with the light and sound levels
        display.SetText(String.Format("Thingy\nLight: {0}", actualAmbientLight));
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

// This Map function is based on the Arduino Map function
// http://www.arduino.cc/en/Reference/Map
private int Map(int src, int in_min, int in_max, int out_min, int out_max)
{
    return (src - in_min) * (out_max - out_min) / (in_max - in_min) + out_min;
}
{% endhighlight %}

If you want to compare your code with the master lab code, you can find it [in the __ThingLabs - Thingy4Windows__ GitHub repo here](https://github.com/ThingLabsIo/Thingy4Windows/blob/master/Thingy/Thingy/StartupTask.cs).

# Conclusion &amp; Next Steps
Congratulations! 

1. ?
2. ?
3. ?

In the [next lab][nextlab] you will set up an Azure IoT Hub to use with the ThingLabs Thingy&trade;. 

<a class="radius button small" href="{{ site.url }}/workshop/thingy-4-windows/setup-azure-iot-hub/">Go to 'Setting Up Azure IoT' ›</a>

[nextlab]: /workshop/thingy-4-windows/setup-azure-iot-hub/