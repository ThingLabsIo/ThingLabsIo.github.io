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
permalink: /workshop/full-day-windows/nightlight/
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

If you haven't already done so, follow the setup instructions at ['Setting Up Your Raspberry Pi 2']({{ site.url }}/workshop/full-day-windows/setup-rpi2/).

# Connecting the Sensors
In this lab you will combine the output device you previously used (a Grove LED module) with an input sensor - a Grove Light Sensor. You will use the measurement of ambient light from the Light Sensor to control the intensity of the LED. Basically you are making a nightlight. 

The Grove Light Snesor is made up of a photoresistor and a 10k Ohm resistor. A _photoresistor_, also known as _light-dependent resistor (LDR)_ or a photocell, works by limiting the amount of voltage that passes through it based on the intensity of light detected. The resistance decreases as light input increases - in other words, the more light, the more voltage passes through the photoresistor.

![Connect the LED and Light Sensor](/images/workshops/full-day-windows/nightlight.jpg)

{% highlight csharp %}
using System;
using Windows.ApplicationModel.Background;

// Add using statements to the GrovePi libraries
using GrovePi;
using GrovePi.Sensors;

namespace Nightlight
{
    public sealed class StartupTask : IBackgroundTask
    {
        public void Run(IBackgroundTaskInstance taskInstance)
        {
            // Connect the Light Sensor to analog port 2
            ILightSensor lightSensor = DeviceFactory.Build.LightSensor(Pin.AnalogPin2);

            // Connect the LED to digital port 5
            ILed led = DeviceFactory.Build.Led(Pin.DigitalPin5);

            // Create a variable to track the LED brightness
            double brightness = 0;

            // Capture the current value from the Light Sensor
            double actualAmbientLight = 0;

            // Decide an a level of ambient light at which the LED should
            // be in a completely off state (e.g. sensorValue == 700)
            int ambientLightThreshold = 700;

            // Loop endlessly
            while (true)
            {
                try {
                    // Capture the current value from the Light Sensor
                    actualAmbientLight = lightSensor.SensorValue();

                    
                    if (actualAmbientLight < ambientLightThreshold)
                    {
                        // Use a range mapping method to conver the difference between the 
                        // actual ambient light and the threshold to a value between 0 and 255
                        // (the 8-bit range of the LED). 
                        // If actual ambient light is low, the differnce between it and the threshold will be
                        // high resulting in a high brightness value.
                        brightness = Map(ambientLightThreshold - actualAmbientLight, 0, ambientLightThreshold, 0, 255);
                    }
                    else
                    {
                        // If the sensorValue is above the level that the LED should 
                        // be completely off. Set the brightness to 0
                        brightness = 0;
                    }

                    // For debuggin, output the values to the Output Window
                    System.Diagnostics.Debug.WriteLine(
                        "Light Value: " + actualAmbientLight + 
                        " : brightness: " + brightness);

                    // AnalogWrite uses Pulse Width Modulation (PWM) to 
                    // control the brightness of the digital LED.
                    led.AnalogWrite(Convert.ToByte(brightness));
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
        }

        private double Map(double src, double in_min, double in_max, double out_min, double out_max)
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

<a class="radius button small" href="{{ site.url }}/workshop/full-day-windows/indoor-environment/">Go to 'Indoor Environment Station' ›</a>

[nextlab]: /workshop/full-day-windows/indoor-environment/