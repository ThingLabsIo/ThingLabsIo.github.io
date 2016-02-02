---
layout: page-fullwidth
title: "Hello, Windows IoT!"
subheadline: "Building Connected Things with Windows 10 IoT Core and C#"
teaser: "In this lab you will create a simple 'Thing' using Windows 10 IoT Core."
show_meta: true
comments: true
header: no
breadcrumb: true
categories: [raspberry-pi, windows-10, dragonboard, minnowboard, c#, iot, maker]
permalink: /tr22/cs/hello-windows-iot/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will create a simple _Thing_ using a Windows 10 IoT device and the Universal Windows Platform. 

# Wire Up the Device
The Raspberry Pi 2 (RPi2) connects to the physical world through the GPIO pins. GPIO stands for General Purpose Input/Output and refers to the two rows of pins on the RPi2. The GPIO pins are a physical interface between the RPi2 and the physical world. Through your app you can designate pins to either receive input or send output. The inputs can be from switches, sensors or other devices. The outputs can be LEDs, servos, motors or countless other devices. Twenty-six of the 40 pins are GPIO pins; the others are power, ground, or reserved pins.

![Raspberry Pi 2 pin Map](/images/rpi2/rpi12_pinout.png)

The RPi2 for this workshop should already be wired for this lab and others - it has multiple circuits wired up; the circuit used for this lab is as shown here (take a minute to examine the RPi2 wiring and identify this circuit):

![Blinky/Hello, World Wiring](/images/rpi2/rpi2_hello_windows_bb.png)

1. GPIO 12 is connected to the positive (longer) lead on the LED. In the app you build, you will control whether or not GPIO pin 12 sends voltage over the circuit.
2. The negative (shorter) lead on the LED is connected to a resistor to reduce the amount of voltage pulled through the circuit.
3. The other end of the resistor is connected to one of the ground GPIO pins, completing the circuit.

The LED will light up when current is passed through the circuit by the app you write; however, you haven't written the Windows 10 app for it yet, so for now it won't do anything.

# Create an Application using the Universal Windows Platform
A Universal Windows app is a Windows experience that is built upon the Universal Windows Platform (UWP), which was first introduced in Windows 8 as the Windows Runtime. The UWP enables you to write an app that targets a device family, such as IoT devices. In fact, the universal app that you write may be able to run on multiple devices families, depending on the device characteristics that it takes advantage of. In this lab you will create a universal app targeting IoT devices running Windows 10. Technically this could be nearly any device, such as a phone, a tablet or a RPi2, however; the universal app you write will access the General Purpose Input/Output (GPIO) of the device, so the app won't actually be compatible with devices that don't have a GPIO.   

## Create a Blank Universal App

1. Launch Visual Studio and start a new __Blank App (Universal Windows)__ (found in the _C# -> Windows -> Universal_ node).
2. Name the application with your alias followed by _-HelloWindowsIoT_ (e.g. _dseven-HelloWindowsIoT_).

![Create a blank Universal Windows Application](/images/rpi2/rpi2_new_universal.png)

## Add the Windows IoT Extensions for the UWP
The _Windows IoT Extenstions for the UWP_ are not included in a new Blank Application by default. The IoT extensions enable namespaces, such as <code>Windows.Devices.Gpio</code> to be referenced and uses in the application. You must add a reference to the _Windows IoT Extensions for the UWP_.

 1. Click on the _Project_ menu and select _Add Reference_
 2. In the Reference Manager dialog, expand the _Universal Windows_ node and select _Extensions_
 3. In the list of extensions, __CHECK THE BOX__ next to _Windows IoT Extensions for the UWP_ and click __OK__ (make sure to select the same version number as the OS running on the RPi2). It is easy to accidently select the _Windows Mobile Extensions for the UWP_ (which is just below the IoT extensions) - pay close attention and make sure you have added the correct reference.
 
![Add the Windows IoT Extensions for the UWP](/images/rpi2/rpi2_install_iotextensions.png)

## Design the App UI
This application has a UI that duplicates what is happening with the hardware (it has a blinking virtual LED on-screen).

1. Open the _MainPage.xaml_ file (this is the layout definition for the initial page that loads when the app is run). 
1. Replace the <code>&lt;Grid&gt;...&lt;/Grid&gt;</code> code with the following:

{% highlight xml %}
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <StackPanel HorizontalAlignment="Center" VerticalAlignment="Center">
        <Ellipse x:Name="LedGraphic" Fill="LightGray" Stroke="White" Width="100" Height="100" Margin="10"/>
        <TextBlock x:Name="DelayText" Text="500ms" Margin="10" TextAlignment="Center" FontSize="26"/>
        <TextBlock x:Name="GpioStatus" Text="Waiting to initialize GPIO..." Margin="10,50,10,10" TextAlignment="Center" FontSize="26"/>
    </StackPanel>
</Grid>
{% endhighlight %}

<blockquote>
For the TechReady 22 labs, we don't have monitors connected to the RPi2 so you will not see this UI. When you run these labs at home, if you connect your RPi2 to a monitor you will have a UI that coincides with the hardware.
</blockquote>

## Code the App Logic
Throughout this lab you will use a feature in Visual Studio called _light bulbs_. Light bulbs are a new productivity feature in Visual Studio 2015. They are icons that appear in the Visual Studio editor and that you can click to perform quick actions including refactoring fixing errors. Light bulbs bring error-fixing and refactoring assistance into a single focal point, often right on the line where you are typing. As you write the code in this lab you will add calls to methods that don't yet exist. The editor will indicate this to you by putting a red "squiggle" underline beneath the method call. When you hover over the offending code a light bulb will appear and you can expand it to see options for generating the missing method. 

### Add 'using' Statements
1. Open the _MainPage.xaml.cs_ file. This is the code behind the layout for the MainPage.xaml. 
2. Add the following to the _using_ statements to the end of the list of similar _using_ statements at the top of the file. 

{% highlight csharp %}
// Enable asynchronous tasks
using System.Threading.Tasks;
// Enable access to the GPIO bus on the RPi2
using Windows.Devices.Gpio;
{% endhighlight %}

### Define Class-level Constants and Variables
THere are a number of constants and variables that will be using throughout this application. Define these in the <code>MainPage</code> class definition.
 
1. Locate the <code>public sealed partial class MainPage : Page</code> class definition (this defines a class called _MainPage_ and inherits all of the capabilities of the _Page_ class)
2. Add the following constant and variable definitions:

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
The LED connected to GPIO pin 12 will turn on and off at an interval defined by the <code>timer</code> object. The <code>TODO</code> comment is the placeholder for configuring the <code>timer</code>. Following the call to <code>InitializeComponent</code>, configure the <code>timer</code> to raise an event every 500ms (soon you will create an event handler that will do the real work). 

1. Locate the <code>// TODO: Create an instance of a Timer that will raise an event every 500ms</code> comment in the <code>public MainPage()</code> constructor.
2. Replace the comment with the following code. 

{% highlight csharp %}
// Create an instance of a Timer that will raise an event every 500ms
timer = new DispatcherTimer();
timer.Interval = TimeSpan.FromMilliseconds(500);
timer.Tick += Timer_Tick;

// TODO: Initialize the GPIO bus
{% endhighlight %}

The <code>Timer_Tick</code> reference should have a red squiggle under it - this indicates that that event hander doesn't yet exist. 

### Handle the Timer.Tick Event
The <code>timer</code> will raise an event at the interval it was configured with - 500ms in this case. When the _Timer.Tick_ event is fired the application will interogate the current state of the LED and if it is set to _Low_ (off) then it will be changed to _High_ (on) and the virtual LED on the screen will be filled with the color red. If the state is _High_ then it will be switched to _Low_ and the virtual LED will be filled with the color light gray. Finally you will use <code>pin.Write(pinValue)</code> to execute the change on the GPIO bus.

You can create an event handler that will fire every time the _Timer.Tick_ event is raised. You can do this using the Visual Studio _light bulb_ refactoring tool. 

1. Hover the mouse over the _Timer\_Tick_ reference until a light bulb appears. 
2. Click the down arrow and select _Generate method 'MainPage.Timer\_Tick'_ 
3. Add the following code for the _Timer\_Tick_ event handler.

<img src="/images/rpi2/rpi2_lab01_Timer_Tick.PNG"/>

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

Of course, <code>pin</code> is _null_ - you haven't done anything more that simply define it so far (you defined it in the class-level variables as <code>private GpioPin pin;</code>). You need to initialize the GPIO controller to get access to the <code>pin</code>.

### Initialize the GPIO Controller
The GPIO controller is the object that provides access to the GPIO bus; it exposes the GPIO pins that you are connecting physical things to. 

1. Replace the <code>// TODO: Initialize the GPIO bus</code> comment in the <code>public MainPage()</code> constructor with the following: 
2. Just like you did with the _Timer\_Tick_ code earlier, use the refactoring _light bulb_ tool to generate the <code>InitGpioAsync()</code> method. 

{% highlight csharp %}
// Initialize the GPIO bus
InitGpioAsync();
{% endhighlight %}

In the _InitGpioAsync()_ method you will get the instance of the default GPIO controller. If the GPIO controller instance is _null_ then the device the app is running on doesn't support GPIO, and you will display a message on the screen indicating this, and that will be the end of the app functionality. If there is a GPIO controller instance, then you will use it to open the GPIO pin that you have connected to the LED and prepare it for use. Add a _null_ check on the _pin_ instance. If it is not _null_, go ahead and start the timer. The timer will begin invoking the _Timer\_Tick_ event every 500ms. 

<blockquote>
The GpioController.GetDefaultAsync() asynchronous method was added to the Windows IoT Extensions for the UWP in the 10.0.10586 version. If you are running Windows 10 (version 10.0.10240) the GpioController.GetDefaultAsync() method will not work.
</blockquote>

1. Add the following code for the <code>InitGpioAsync()</code> method.
2. Add the <code>async</code> modifier to the method signature.
3. Change the return type from <code>void</code> to <code>Task</code>. 

{% highlight csharp %}
private async Task InitGpioAsync()
{
    // Get the default GPIO controller
    var gpio = await GpioController.GetDefaultAsync();
    
    // If the default GPIO controller is not present, then the device 
    // running this app isn't capable of GPIO operations.
    if (gpio == null)
    {
        pin = null;
        GpioStatus.Text = "There is no GPIO controller on this device.";
        return;
    }
    
    // Open the GPIO channel
    pin = gpio.OpenPin(LED_PIN);

    // As long as the pin object is not null, proceed
    if (pin != null)
    {
        // Define the pin as an output pin
        pin.SetDriveMode(GpioPinDriveMode.Output);
        // Define the initial status as LOW (off)
        pinValue = GpioPinValue.Low;
        // Write the tate to the pin
        pin.Write(pinValue);
        // Update the on screen text to indicate that the GPIO is ready
        GpioStatus.Text = "GPIO pin is initialized correctly.";
        // Start the timer.
        timer.Start();
    }
}
{% endhighlight %}

If you want to compare your code with the master lab code, you can find it [here](https://github.com/ThingLabsIo/IoTLabs/blob/master/RPi2/HelloWindowsIoT/HelloWindowsIoT/MainPage.xaml.cs).

# Run the App on the Device
The application is ready to be deployed and run on the RPi2. You must set the target in Visual Studio to _ARM_ and point the debugger at a _Remote Machine_ (your RPi2).

1. Select __ARM__ from the _Solution Platforms_ list in the toolbar.
2. Select __REMOTE MACHINE__ from the _Device_ dropdown list in the toolbar.

<img src="/images/rpi2/rpi2_lab01_arm.png"/>

You will be prompted with the _Remote Connections_ dialog. 

<blockquote>
The RPi2 you are working with is named <strong>ThingLabsXX</strong> where the XX is replaced with the number on your RPi2.
</blockquote>

1. Select your device from the list of _Auto Detected_ devices - __IF YOUR DEVICE IS NOT LISTED__ type the device name into the _Manual Configuration_ textbox
2. Set the _Authentication Mode_ to __Universal (Unencrypted Protocol)__
3. Click _Select_

<img src="/images/rpi2/rpi2_lab01_remote.png"/>

__NOTE:__ You can verify or modify these values by navigating to the project properties (select Properties in the Solution Explorer) and choosing the Debug tab on the left.

1. Press __F5__ to run the application and you should see it deploy on the RPi2. You will see the red LED blink in unison with the red circle on the screen. If the red LED is not blinking, but the display on the screen is, recheck your wiring. 

# Conclusion &amp; Next Steps

Congratulations! You have built a Universal Windows Platform application that controlled one of the GPIO pins and deployed the app to a RPi2. The core concepts you've learned are:

1. Building a Universal Windows Platform application that can run on any Windows 10 device. 
2. Testing for the existence of the GPIO controller to inform the application of what capabilities are accessible.
3. Controlling the state of a device via the GPIO pins. 

In the [next lab][nextlab] you will set up a Microsoft Azure IoT Hub that will act as the cloud backend for your IoT devices. In the labs after that you will build a new _Thing_ that will collect environment data and send it to your IoT hub.

<a class="radius button small" href="{{ site.url }}/tr22/cs/setup-azure-iot-hub/">Go to 'Setup Azure IoT Hub' ›</a>

[nextlab]: /tr22/cs/setup-azure-iot-hub/