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
 * Grove Sound Sensor x 1 _ connector cable
 * Grove Buzzer x 1 + connector cable
 * Grove RGB LCD Display x 1 + connector cable
4. A Wi-Fi Adapter (choose one from the list [here](http://ms-iot.github.io/content/en-US/win10/SupportedInterfaces.htm#WiFi-Dongles))
5. 8GB micro SD card - class 10 or better. Microsoft suggests one of the following:
	* [Samsung 32GB EVO Class 10 Micro SDHC up to 48MB/s with Adapter (MB-MP32DA/AM)](http://www.amazon.com/gp/product/B00IVPU786)
	* [SanDisk Ultra Micro SDHC, 16GB Card](http://www.amazon.com/SanDisk-Ultra-Micro-SDHC-16GB/dp/9966573445).

If you haven't already done so, complete the previous lab - ['Nightlight']({{ site.url }}/workshop/thingy-4-windows/nightlight/).

# Connecting the Sensors
In this lab you will combine the Nightlight device you previously created (a Grove LED module and a Grove Light Sensor) with several other input sensors and output actuators.


In this application you will read the voltage value coming into the ADC from the voltage divider - the higher the voltage, the darker it is (remember, you are reading in the residual voltage, which is diverted to the ADC when there is resistance in the photoresistor). You will use the _darkness_ value to determine if the LED should be on or off.

The ADC is connected to the RPi2 through the Serial Peripheral Interface (SPI) bus. SPI is a synchronous serial communication interface specification used for short distance communication, primarily in embedded systems. SPI devices communicate in full duplex mode using a master-slave architecture with a single master. The master device originates the frame for reading and writing. Multiple slave devices are supported through selection with individual slave select (SS) lines. SPI is a four-wire serial bus as follows:

1. SCLK - Serial Clock (output from master).
2. MOSI - Master Output, Slave Input (output from master).
3. MISO - Master Input, Slave Output (output from slave).
4. SS - Slave Select (active low, output from master).

We won't go any deeper into SPI or the pin layout of the two ADCs - suffice to say that the ADC is wired up to support the four-channel SPI bus, plus supply voltage and ground. The wire connecting the voltage divider to the ADC is the input channel you will read the residual voltage from. 



# Code for Thingy

{% highlight csharp %}
using System;
using Windows.ApplicationModel.Background;

// Add using statements to the GrovePi libraries
using GrovePi;
using GrovePi.Sensors;
using GrovePi.I2CDevices;
using Windows.System.Threading;

namespace Thingy
{
    public sealed class StartupTask : IBackgroundTask
    {
        /**** DIGITAL SENSORS AND ACTUATORS ****/
        // Connect the buzzer to digital port 2
        IBuzzer buzzer;
        // Connect the button sensor to digital port 4
        IButtonSensor button;
        // Connect the Blue LED to digital port 5
        ILed blueLed;
        // Connect the Red LED to digital port 6
        ILed redLed;

        /**** ANALOG SENSORS ****/
        // Connect the sound sensor to analog port 0
        ISoundSensor soundSensor;
        // Connect the light sensor to analog port 2
        ILightSensor lightSensor;

        /**** I2C Deices ****/
        // Connect the RGB display to one of the I2C ports
        IRgbLcdDisplay display;

        /**** Constants and Variables ****/
        // Decide an a level of ambient light at which the LED should
        // be in a completely off state (e.g. sensorValue == 700)
        const int ambientLightThreshold = 700;
        // Create a variable to track the current red LED brightness
        private int brightness;
        // Create a variable to track the current value from the Light Sensor
        private int actualAmbientLight;
        // Create a variable to track the current ambient noise level
        private int soundLevel;
        // Create a variable to track the state of the button
        private SensorStatus buttonState;
        // Create a timer to control the rateof sensor and actuator interactions
        private ThreadPoolTimer timer;
        // Create a deferral object to prevent the app from terminating
        private BackgroundTaskDeferral deferral;

        public void Run(IBackgroundTaskInstance taskInstance)
        {
            // Get the deferral instance
            deferral = taskInstance.GetDeferral();

            // Instantiate the sensors and actuators
            buzzer = DeviceFactory.Build.Buzzer(Pin.DigitalPin2);
            button = DeviceFactory.Build.ButtonSensor(Pin.DigitalPin4);
            blueLed = DeviceFactory.Build.Led(Pin.DigitalPin5);
            redLed = DeviceFactory.Build.Led(Pin.DigitalPin6);
         
            soundSensor = DeviceFactory.Build.SoundSensor(Pin.AnalogPin0);
            lightSensor = DeviceFactory.Build.LightSensor(Pin.AnalogPin2);
            
            display = DeviceFactory.Build.RgbLcdDisplay();

            // The IO to the GrovePi sensors and actuators can generate a lot
            // of exceptions - wrap all GrovePi API calls in try/cath statements.
            try {
                // Set the RGB backlight to red and display a message
                display.SetBacklightRgb(255, 0, 0);
                display.SetText("The Thingy is getting started");
            }
            catch(Exception ex)
            {
                // On Error, Resume Next :)
            }

            // Start a timer to check the sensors and activate the actuators five times per second
            timer = ThreadPoolTimer.CreatePeriodicTimer(Timer_Tick, TimeSpan.FromMilliseconds(200));
        }

        private void Timer_Tick(ThreadPoolTimer timer)
        {
            try {
                // Capture the current ambient noise level
                soundLevel = soundSensor.SensorValue();
                
                // Check the button state
                if (button.CurrentState != buttonState)
                {
                    buttonState = button.CurrentState == SensorStatus.Off ? SensorStatus.On : SensorStatus.Off;
                    
                    blueLed.ChangeState(buttonState);
                    buzzer.ChangeState(buttonState);
                    
                    // For debugging purposes, log a console message
                    System.Diagnostics.Debug.WriteLine("**** BUTTON STATE: " + buttonState + " ****");
                }
                
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
                
                // Use the brightness value to control the brightness of the RGB LCD backlight
                byte rgbVal = Convert.ToByte(brightness);
                display.SetBacklightRgb(rgbVal, rgbVal, rgbVal);

                // Updae the RGB LCD with the light and sound levels
                display.SetText(String.Format("Thingy\nL:{0} S:{1}", actualAmbientLight, soundLevel));
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

In the [next lab][nextlab] you will set up an Azure IoT Hub to use with the ThingLabs Thingy&trade;. 

<a class="radius button small" href="{{ site.url }}/workshop/thingy-4-windows/setup-azure-iot-hub/">Go to 'Setting Up Azure IoT' ›</a>

[nextlab]: /workshop/thingy-4-windows/setup-azure-iot-hub/