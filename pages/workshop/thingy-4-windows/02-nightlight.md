---
layout: "page-fullwidth"
title: "Nightlight"
subheadline: "Building Connected Things with Windows 10 IoT and Microsoft Azure"
teaser: "In this lab you will create a device that responds to ambient light and changes the intensity of an LED - a nightlight."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [raspberry-pi, windows-10, grove, c#, iot, maker]
permalink: /workshop/windows-thingy/nightlight/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will create a simple _Thing_ using a Windows 10 IoT device and the Universal Windows Platform. 

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

If you haven't already done so, follow the setup instructions at ['Setting Up Your Raspberry Pi 2']({{ site.url }}/workshop/windows-thingy/setup-rpi2/).

# Connecting the Sensors
In this lab you will combine the output device you previously used (a Grove LED module) with an input sensor - a Grove Light Sensor. You will use the measurement of ambient light from the Light Sensor to control the intensity of the LED. Basically you are making a nightlight. 

The Grove Light Snesor is made up of a photoresistor and a 10k Ohm resistor. A _photoresistor_, also known as _light-dependent resistor (LDR)_ or a photocell, works by limiting the amount of voltage that passes through it based on the intensity of light detected. The resistance decreases as light input increases - in other words, the more light, the more voltage passes through the photoresistor.

![Connect the LED and Light Sensor](/images/workshops/windows-thingy/nightlight.jpg)

{% highlight csharp %}
using System;
using Windows.ApplicationModel.Background;

// Add using statements to the GrovePi libraries
using GrovePi;
using GrovePi.Sensors;
using Windows.System.Threading;

namespace Nightlight
{
    public sealed class StartupTask : IBackgroundTask
    {
        /**** DIGITAL SENSORS AND ACTUATORS ****/
        // Connect the Red LED to digital port 6
        ILed redLed;

        /**** ANALOG SENSORS ****/
        // Connect the light sensor to analog port 2
        ILightSensor lightSensor;
        
        /**** Constants and Variables ****/
        // Decide an a level of ambient light at which the LED should
        // be in a completely off state (e.g. sensorValue == 700)
        const int ambientLightThreshold = 700;
        // Create a variable to track the current LED brightness
        private int brightness;
        // Create a variable to track the current value from the Light Sensor
        private int actualAmbientLight;
        // Create a timer to control the rateof sensor and actuator interactions
        ThreadPoolTimer timer;
        // Create a deferral object to prevent the app from terminating
        BackgroundTaskDeferral deferral;

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

        // This Map function is based on the Arduino Map function
        // http://www.arduino.cc/en/Reference/Map
        private int Map(int src, int in_min, int in_max, int out_min, int out_max)
        {
            return (src - in_min) * (out_max - out_min) / (in_max - in_min) + out_min;
        }
    }
}
{% endhighlight %}


# Conclusion &amp; Next Steps
Congratulations! 

1. ?
2. ?
3. ?

In the [next lab][nextlab] you will build the device that you will use for the rest of this workshop - an indoor environment station. 

<a class="radius button small" href="{{ site.url }}/workshop/windows-thingy/thingy/">Go to 'ThingLabs Thingy&trade;' ›</a>

[nextlab]: /workshop/windows-thingy/thingy/