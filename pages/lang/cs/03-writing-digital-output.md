---
layout: "page-cs"
title: "Writing Digital Output"
subheadline: "Building Connected Things with Windows 10 IoT and C#"
teaser: "In this lab you will create a simple 'Thing' using Windows 10 IoT Core."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [raspberry-pi, windows-10, dragonboard, minnowboard, c#, iot, maker]
permalink: /lang/cs/writing-digital-output/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will create a simple _Thing_ using a Windows 10 IoT device and the Universal Windows Platform. 

# Bill of Materials
What you will need:

1. One of the following development boards:
    * [Raspberry Pi 2 - $42.00](http://www.amazon.com/Raspberry-Pi-Model-Project-Board/dp/B00T2U7R7I/) with a [5V 2A Switching Power Supply w/ 20AWG 6' MicroUSB Cable - $7.95](https://www.adafruit.com/product/1995)
    * [DragonBoard 410c - $75.00](http://partners.arrow.com/campaigns-na/qualcomm/dragonboard-410c/) with a [WM24P-12-A-QL 12V 2A Switching Power Supply - $11.24](https://www.arrow.com/en/products/wm24p-12-a-ql/autec-power-systems#page-1)
3. [(2) Jumper wires (Male to Female) - $1.95](https://www.adafruit.com/product/1954)
4. [(1) Red LED - 25 for $8.00](http://www.adafruit.com/products/297)
5. [(1) 330 Ohm resistors - 100 for $3.90](http://www.amazon.com/E-Projects-Resistors-Watt-330R-Pieces/dp/B00BVOR6IS/)

The devices should be configured according to thier specific instructions.

    * ['Setting Up Your Raspberry Pi 2']({ site.url }}/lang/cs/setup-rpi2/)
    * ['Setting Up Your DragonBoard 410c']({ site.url }}/lang/cs/setup-dragon/)
 
# Wire Up the Device
The RPi2 connects to the physical world through the GPIO pins. GPIO stands for General Purpose Input/Output and refers to the two rows of pins on RPI2. The GPIO pins are a physical interface between the RPi2 and the physical world. Through your app you can designate pins to either receive input or send output. The inputs can be from switches, sensors or other devices. The outputs can be LEDs, servos, motors or countless other devices. Twenty-six of the 40 pins are GPIO pins; the others are power, ground, or reserved pins.

<img src="/images/rpi2/rpi12_pinout.png"/>

Wire up the RPi2 according to this diagram.

<img src="/images/rpi2/rpi2_lab01_bb.png"/>

1. GPIO pin 12 is connected to the positive (longer) lead on the LED. In the app you build, you will control whether or not GPIO pin 12 sends voltage over the circuit.
2. The negative (shorter) lead on the LED is connected to a resistor to reduce the amount of voltage pulled through the circuit.
3. The other end of the resistor is connected to one of the ground GPIO pins, completing the circuit.

The LED will light up when current is passed through the circuit. 

# Create an Application using the Universal Windows Platform
A Universal Windows app is a Windows experience that is built upon the Universal Windows Platform (UWP), which was first introduced in Windows 8 as the Windows Runtime. The UWP enables you to write an app that targets a device family, such as IoT devices. In fact, the universal app that you write may be able to run on multiple devices families, depending on the device characteristics that it takes advantage of. In this lab you will create a universal app targeting IoT devices running Windows 10. Technically this could be nearly any device, such as a phone, a tablet or a RPi2, however; the universal app you write will access the General Purpose Input/Output (GPIO) of the device, so the app won't actually be compatible with devices that don't have a GPIO.   

## Create a Blank Universal App
Launch Visual Studio and start a new __Blank App (Universal Windows)__ (found in the _C# -> Windows -> Universal_ node).

Name the application _HelloWindowsIoT_.

<img src="/images/rpi2/rpi2_new_universal.png"/>

## Add the Windows IoT Extensions for the UWP
Once the solution is created, click on the _Project_ menu and select _Add Reference_.

In the Reference Manager dialog, expand the _Universal Windows_ node and select _Extensions_.

In the list of extensions, check the box next to __Windows IoT Extensions for the UWP__ and click __OK__.

<img src="/images/rpi2/rpi2_install_iotextensions.png"/>

## Design the App UI
Open the _MainPage.xaml_ file. This is the layout definition for the initial page that loads when the app is run. Next you will add a few elements to the page.

{% highlight xml %}
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <StackPanel HorizontalAlignment="Center" VerticalAlignment="Center">
        <Ellipse x:Name="LedGraphic" Fill="LightGray" Stroke="White" Width="100" Height="100" Margin="10"/>
        <TextBlock x:Name="DelayText" Text="500ms" Margin="10" TextAlignment="Center" FontSize="26"/>
        <TextBlock x:Name="GpioStatus" Text="Waiting to initialize GPIO..." Margin="10,50,10,10" TextAlignment="Center" FontSize="26"/>
    </StackPanel>
</Grid>
{% endhighlight %}

## Code the App Logic
Open the _MainPage.xaml.cs_ file. This is the code behind the layout for the MainPage.xaml. Add the following to the _using_ statements at the top of the file. 

{% highlight csharp %}
using Windows.Devices.Gpio;
{% endhighlight %}

Add the following variable definitions inside the <code>public sealed partial class MainPage : Page</code> class definition:

{% highlight csharp %}
public sealed partial class MainPage : Page
{
        // Define the physical pin connected to the LED.
        private const int LED_PIN = 12;
        // Deifne a variable to represent the pin as an object.
        private GpioPin pin;
        // Define a variable to hold the value of the pin (HIGH or LOW).
        private GpioPinValue pinValue;
        // Define a time used to control the frequency of events.
        private DispatcherTimer timer;
        // Define a color brushes for the on screen representation of the LED.
        private SolidColorBrush redBrush = new SolidColorBrush(Windows.UI.Colors.Red);
        private SolidColorBrush grayBrush = new SolidColorBrush(Windows.UI.Colors.LightGray);

        public MainPage()
        {
            this.InitializeComponent();
            
            // TODO: Create an instance of a Timer that will raise an event every 500ms
        }
}
{% endhighlight %}

### Create a Timer to Control the LED
Following the call to <code>InitializeComponent</code>, create a _Timer_ that will raise an event every 500ms.

{% highlight csharp %}
// Create an instance of a Timer that will raise an event every 500ms
timer = new DispatcherTimer();
timer.Interval = TimeSpan.FromMilliseconds(500);
timer.Tick += Timer_Tick;
// TODO: Initialize the GPIO bus
{% endhighlight %}

Using the Visual Studio refactoring tools, you can generate the method stub for the __Timer\_Tick__ event handler. Hover over the _Timer\_Tick_ text until a lightbulb appears. Click the down arrow and select _Generate method 'MainPage.Timer\_Tick'_ 

<img src="/images/rpi2/rpi2_lab01_Timer_Tick.PNG"/>

Add the following code for the _Timer\_Tick_ event handler.

{% highlight csharp %}
private void Timer_Tick(object sender, object e)
{
    // This Timer event will be raised on each timer interval (defined above)
            
    if (pinValue == GpioPinValue.Low)
    {
        // If the current state of the pin is LOW (off), then set it to HIGH (on)
        // and update the on screen UI to represent the LED in the on state
        pinValue = GpioPinValue.High;
        LedGraphic.Fill = redBrush;
    }
    else
    {
        // If the current state of the pin is HIGH (on), then set it to LOW (off)
        // and update the on screen UI to represent the LED in the off state
        pinValue = GpioPinValue.Low;
        LedGraphic.Fill = grayBrush;
    }
    // Write the state to to pin
    pin.Write(pinValue);
}
{% endhighlight %}

### Initialize the GPIO Controller
The next thing to do is initialize the GPIO controller. Back in the _MainPage()_ constructor, following the timer code, make a call to a method that you haven't defined yet called <code>InitGpio()</code>. 

{% highlight csharp %}
// Initialize the GPIO bus
InitGpio();
            
// TODO: As long as the pin object is not null, proceed with the timer.                        
{% endhighlight %}

Just like you did with the _Timer\_Tick_ code, use the refactoring _lightbulb_ tool to generate the <code>InitGpio()</code> method. In the _InitGpio()_ method you will get the instance of the default GPIO controller - the object that brokers all communication between your app and the GPIO bus. 

If the GPIO controller instance is _null_ then the device the app is running on doesn't support GPIO, and you will display a message on the screen indicating this, and that will be the end of the app functionality. If there is a GPIO controller instance, then you will use it to open the GPIO pin that you have connected to the LED and prepare it for use. Lastly you will display a message that the GPIO pin is initialized. 

{% highlight csharp %}
private void InitGpio()
{
    // Get the default GPIO controller
    var gpio = GpioController.GetDefault();
    // If the default GPIO controller is not present, then the device 
    // running this app isn't capable of GPIO operations.
    if (gpio == null)
    {
        pin = null;
        GpioStatus.Text = "There is no GPIO controller on this device.";
        return;
    }
    // Open the GPIO pin channel
    pin = gpio.OpenPin(LED_PIN);
    // Define the pin as an OUTPUT pin
    pin.SetDriveMode(GpioPinDriveMode.Output);
    // Define the initial state as LOW (off)
    pinValue = GpioPinValue.Low;
    // Write the state to to pin
    pin.Write(pinValue);
    // Update the on screen text to indicate that GPIO is ready
    GpioStatus.Text = "GPIO pin initialized correctly.";
}
{% endhighlight %}

### Check for the Existence of the GPIO Pin Object and Start the Timer
Back in the _MainPage()_ constructor, add a _null_ check on the _pin_ instance (remember, it will be _null_ if the GPIO controller was null). If it is not _null_, go ahead and start the timer. The timer will begin invoking the _Timer\_Tick_ event every 500ms.

This is what the _MainPage()_ constructor should look like when completed.

{% highlight csharp %}
public MainPage()
{
    this.InitializeComponent();
            
    // Create an instance of a Timer that will raise an event every 500ms
    timer = new DispatcherTimer();
    timer.Interval = TimeSpan.FromMilliseconds(500);
    timer.Tick += Timer_Tick;
            
    // Initialize the GPIO bus
    InitGpio();
            
    // As long as the pin object is not null, proceed with the timer.
    if (pin != null)
    {
        timer.Start();
    }
}
{% endhighlight %}

If you want to compare your code with the master lab code, you can find it [here](https://github.com/ThingLabsIo/IoTLabs/blob/master/RPi2/Lab01/Lab01/MainPage.xaml.cs).

# Run the App Locally
Press __F5__ to run the app locally. You should see a screen similar to this:

<img src="/images/rpi2/rpi2_lab01_nogpio.png"/>

This screen is displayed because your development machine doesn't have a GPIO controller.

# Run the App on the Device
To deploy this application to your RPi2, select __ARM__ from the _Solution Platforms_ list in the toolbar, and select __REMOTE MACHINE__ from the _Device_ dropdown list in the toolbar.

<img src="/images/rpi2/rpi2_lab01_arm.png"/>

You will be prompted with the _Remote Connections_ dialog. Select your device from the list of _Auto Detected_ devices, or type in the device name or IP address into the _Manual Configuration_ textbox (set the _Authentication Mode_ to __None__) and click _Select_.

<img src="/images/rpi2/rpi2_lab01_remote.png"/>

__NOTE:__ You can verify or modify these values by navigating to the project properties (select Properties in the Solution Explorer) and choosing the Debug tab on the left.

Now press __F5__ to run the application and you should see it deploy on the RPi2. You will see the red LED blink in unison with the red circle on the screen. If the red LED is not blinking, but the display on the screen is, recheck your wiring. 

# Conclusion &amp; Next Steps

Congratulations! You have built a Universal Windows Platform application that controlled one of the GPIO pins and deployed the app to a Raspberry Pi. The core concepts you've learned are:

1. Building a Universal Windows Platform application that can run on any Windows 10 device. 
2. Testing for the existence of the GPIO controller to inform the application of what capabilities are accessible.
3. Controlling the state of a device via the GPIO pins. 

In the [next lab][nextlab] you will set up a Microsoft Azure IoT Hub that will act as the cloud backend for your IoT devices. In the labs after that you will build a new _Thing_ that will collect environment data and send it to your IoT hub.

[Next Lab ->][nextlab]

{% include next-previous-post-in-category.html %}

[nextlab]: /rpi2/02/