---
layout: "page-fullwidth"
title: "ThingLabs Thingy&trade;"
subheadline: "Building Connected Things with Windows 10 IoT and Microsoft Azure"
teaser: "In this lab, you will build a device with multiple sensors and actuators."
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

In this lab, you will expand on the Nightlight you created in the previous lab and build the __ThingLabs Thingy&trade;__. In the following labs you will use the ThingLabs Thingy&trade; to connect to Azure IoT services and track all of this data.

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
Connect the sensors and actuators to the GrovePi shield as illustrated here:

![Connect the Sensors](/images/workshops/thingy-4-windows/thingy.jpg)

* Connect the __red__ LED module to D6
* Connect the __blue__ LED module to D5
* Connect the Button module to D4
* Connect the Buzzer module to D2
* Connect the Light Sensor to A2
* Connect the RGB LCD Display to one of the I2C ports.

# Build the App
Like the ['Hello, Windows IoT!'](../hello-windows-iot/) and ['Nightlight'](../nightlight/) labs, the _ThingLabs Thingy&trade;_ application is a _Background Application (IoT)_ project in Visual Studio. 

1. Launch Visual Studio and start a new __Background Application (IoT)__ (found in the _Templates -> C# -> Windows -> Windows IoT Core_ node).
2. Name the application __Thingy__.
3. Add a reference to the GrovePi libraries the same way you did in the previous labs.
   * To install GrovePi for Windows IoT, ensure __thinglabs__ is the selected _Package Source_ and run the following command from the __Package Manager Console__

    ```
    Install-Package GrovePi
    ```
    
    * Verify that GrovePi v1.0.7 was installed but reading the log in the Package Manager Console.
    
4. Open the __StartupTask.cs__ file. Add the following to the __using__ statements at the top of the file. 

{% highlight csharp %}
// Add using statements to the GrovePi libraries
using GrovePi;
using GrovePi.Sensors;
using GrovePi.I2CDevices;
using Windows.System.Threading;
{% endhighlight %}

## Define the Class-level Variables for the Thingy

### Sensor Variables
There are five (5) class-level variables you will use to refer to the physical sensors and actuators:

* Digital Sensors and Actuators - sensors and actuators that have on/off (i.e. 0 or 1) states.
  * __buzzer__ - an _IBuzzer_ instance that derives from `GrovePi.Sensor` and exposes properties to get the current status of the buzzer and change its state. 
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
// Connect the buzzer to digital port 2
IBuzzer buzzer;
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

### State and Other Variables
There are six (6) class-level variables you will use in this application:

* __ambientLightThreshold__ - an _int_ constant between 0 (dark) and 1023 (bright) that defines the measurement of ambient light at which the LED should be in a completely off state.
* __brightness__ - an _int_ variable to track the current LED brightness.
* __actualAmbientLight__ - an _int_ variable to track the current value of ambient light as measured by the light sensor.
* __buttonState__ - a _SensorStatus_ variable to track the state of the button.
* __timer__ - a _ThreadPoolTimer_ instance that will control the rate of sensor and actuator interactions. 
* __deferral__ - a _BackgroundTaskDeferral_ instance that will allow the application to continue to run even after the `Run()` method has completed.

These are class-level variables that will be used primarily to track state of the sensors.
 
1. Add the following class-level variable definitions immediately after the preceeding variables.

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

## Instantiate the Objects and Start the Timer
Inside the `Run(IBackgroundTaskInstance taskInstance)` method, you need to instantiate all of the objects you defined, including the timer. The time will begin _ticking_ as soon as it is created. Within the timer's _TimePeriodElapsed_ callback, you will do all of the interaction with the sensors and actuators.

1. Modify the `Run(IBackgroundTaskInstance taskInstance)` method as follows: 

{% highlight csharp %}
public void Run(IBackgroundTaskInstance taskInstance)
{
    // Get the deferral instance
    deferral = taskInstance.GetDeferral();
    
    // Instantiate the sensors and actuators
    buzzer = DeviceFactory.Build.Buzzer(Pin.DigitalPin2);
    button = DeviceFactory.Build.ButtonSensor(Pin.DigitalPin4);
    blueLed = DeviceFactory.Build.Led(Pin.DigitalPin5);
    redLed = DeviceFactory.Build.Led(Pin.DigitalPin6);
    lightSensor = DeviceFactory.Build.LightSensor(Pin.AnalogPin2);
    display = DeviceFactory.Build.RgbLcdDisplay();
    
    // Initialize the button state to 'off'
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
{% endhighlight %}

## Handle the TimePeriodElapsed Event
As with the [Nightlight lab](../nightlight/), you will handle measuring light data and setting the LED brightness in the timer's _TimePeriodElapsed_. You will also poll the _Thingy_ for button state changes and handle them by doing the following:

1. Evaluate the `button.CurrentState` against the `buttonState` state variable. If there is a state change...
  * Update the `buttonState` value to the new state.
  * Use the `ChangeState()` method to change the state of the __blue__ LED and the buzzer.
2. Get the light value from the light sensor by calling `lightSensor.SensorValue()`.
3. Evaluate the light value against the `ambientLightThreshold` value.
4. If the light value is below the threshold (i.e. dark enough that the LED should be illuminated), map the difference between the light value and the threshold to an 8-bit range (0-255).
5. Use the value derived above to set the intensity/brightness of the LED.
6. Set the background color of the LCD display between blue (bright light) and white (no light).
7. Set the text of the LCD display to show the current light measurement. 

## Interact with Sensors Using the Timer
Where you created the timer in the `ThreadPoolTimer.CreatePeriodicTimer(Timer_Tick, TimeSpan.FromMilliseconds(200))` call do the following:

1. Hover the mouse over the `Timer_Tick` reference until a light bulb appears.
2. Click the down arrow and select __Generate method ‘StartupTask.Timer_Tick’__.
3. Add the following code for the Timer_Tick method.

{% highlight csharp %}
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
            buzzer.ChangeState(buttonState);
        }
        
        // Capture the current value from the Light Sensor
        actualAmbientLight = lightSensor.SensorValue();
        
        // Log the amount of resistance the light sensor is providing.
        System.Diagnostics.Debug.WriteLine("Light Sensor Resistance: " + lightSensor.Resistance() + " V");

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

If you want to compare your code with the master lab code, you can find it [in the __ThingLabs - Thingy4Windows__ GitHub repo here](https://github.com/ThingLabsIo/Thingy4Windows/blob/master/Thingy/Thingy/StartupTask.cs).

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
Congratulations! You have created the ThingLabs Thingy&trade;. In the following labs you will use the Thingy to connect to Azure IoT services and track all of this data. The concepts you learned in this lab are:

1. Working with multiple sensors and actuators.
2. Maintaining state while constantly polling sensors for state changes.
3. Making annoying noises with buzzers. 

In the [next lab][nextlab] you will set up an Azure IoT Hub to use with the ThingLabs Thingy&trade;. 

<a class="radius button small" href="{{ site.url }}/workshop/thingy-4-windows/setup-azure-iot-hub/">Go to 'Setting Up Azure IoT' ›</a>

[nextlab]: /workshop/thingy-4-windows/setup-azure-iot-hub/