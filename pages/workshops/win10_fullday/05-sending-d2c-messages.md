---
layout: "page-fullday-windows"
title: "Sending Telemetry to the Cloud"
subheadline: "Building Connected Things with Windows 10 IoT and Microsoft Azure"
teaser: "In this lab you will build a Universal Windows Platform application that collects ambient light data and sends it to the Cloud."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [raspberry-pi, windows-10, dragonboard, minnowboard, c#, iot, maker, azure, azure-iot-hub]
permalink: /workshop/fullday-windows/sending-d2c-messages/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will build a Universal Windows Platform application that detects ambient light and sends the data that is being collected to Azure IoT Hub. In following lab you will build a data pipeline to process the incoming data stream and output it to a visualization tool.

# Bill of Materials
What you will need:

1. One of the following development boards:
    * [Raspberry Pi 2](http://www.amazon.com/Raspberry-Pi-Model-Project-Board/dp/B00T2U7R7I/) with a [5V 2A Switching Power Supply w/ 20AWG 6' MicroUSB Cable](https://www.adafruit.com/product/1995)
    * [DragonBoard 410c](http://partners.arrow.com/campaigns-na/qualcomm/dragonboard-410c/) with a [WM24P-12-A-QL 12V 2A Switching Power Supply](https://www.arrow.com/en/products/wm24p-12-a-ql/autec-power-systems#page-1)
3. [Jumper wires (Male to Male)](https://www.adafruit.com/product/1957)
4. [Jumper wires (Male to Female)](https://www.adafruit.com/product/1954)
5. [Photoresistor](https://www.adafruit.com/products/161)
6. [Red and Green (or White) LEDs](http://www.adafruit.com/products/297)
7. [A few 330 Ohm resistors](http://www.amazon.com/E-Projects-Resistors-Watt-330R-Pieces/dp/B00BVOR6IS/)
8. [A 10k Ohm resistor](http://www.amazon.com/E-Projects-10k-Resistors-Watt-Pieces/dp/B00BWYS9BA/)
9. Oe of the following analog-digital-converters:
	* [MCP3208 - 8-Channel 12-Bit ADC with SPI Interface](http://www.digikey.com/product-detail/en/MCP3208-CI%2FSL/MCP3208-CI%2FSL-ND/305929)
	* [MCP3008 - 8-Channel 10-Bit ADC with SPI Interface](https://www.adafruit.com/product/856)
	* [MCP3002 - 2-Channel 10-Bit ADC with SPI Interface](https://www.sparkfun.com/products/8636)
10. 8GB micro SD card - class 10 or better. Microsoft suggests one of the following:
	* [Samsung 32GB EVO Class 10 Micro SDHC up to 48MB/s with Adapter (MB-MP32DA/AM)](http://www.amazon.com/gp/product/B00IVPU786)
	* [SanDisk Ultra Micro SDHC, 16GB Card](http://www.amazon.com/SanDisk-Ultra-Micro-SDHC-16GB/dp/9966573445).

To make life easy, you can get these components and more in the [Microsoft IoT Pack for Raspberry Pi 2](http://www.adafruit.com/windows10iotpi2) from AdaFruit.
 
* ['Setting Up Your Raspberry Pi 2']({{ site.url }}/lang/cs/setup-rpi2/)
* ['Setting Up Your DragonBoard 410c']({{ site.url }}/lang/cs/setup-dragon/)
 
# Capturing Analog Data with a Voltage Divider
For this lab you will work with a few new concepts - both in the circuits connected to the Raspberry Pi 2 (RPi2) and in the Cloud. The first thing you will do is wire up the RPi2 to be able to read voltage as determined by the resistance created by a photoresistor. Wire your board according to the diagram (wire colors don't matter, but help with identification of purpose). This wiring uses an analog-to-digital-converter (ADC) - either an MCP3208 or an MCP3002, depending on what you have - which enables you to capture analog input instead of simply digital input. When you did the lab with the LED you dealt only with a digital signal - you sent voltage to the LED to turn it on, or off (with no voltage). Many sensors, such as a _photoresistor_, are capable of analog input or output, giving them a broader range than simply a 1 or a 0. 

A _photoresistor_, also known as _light-dependent resistor (LDR)_ or a photocell, works by limiting the amount of voltage that passes through it based on the intensity of light detected. The resistance decreases as light input increases - in other words, the more light, the more voltage passes through the photoresistor.

In order to take advantage of the photoresistor you will create a _voltage divider_ - a passive linear circuit that splits the input voltage amongst two or more components (similar to a Y-splitter). The following schematic shows a voltage divider in use on an Arduino Uno R3 (this is a simpler way to show the diagram as compared ot the RPi2 which, as you will see, incorporates an external ADC).

<img src="/images/photoresistor_schem.png"/>

To create the voltage divider needed for this lesson you will:

- Connect the voltage from the 5-volt (input voltage) pin to a circuit (using a breadboard).
- Connect the input voltage to a static resistor (10k Ohm).
- Establish a voltage divider coming out of the static resistor:
   - One route to the ADC which is connected to the RPi2.
   - One route to a variable resistor (the photoresistor).
- Completing the circuit out of the variable resistor to ground.

As the photoresistor increases its resistance (lower light intensity) more of the input voltage coming through the circuit is diverted to the ADC. That means that the less intense the light into the photoresistor the more resistance it creates, which in turn diverts more voltage to the ADC (the current has to go somewhere). Likewise, the more intense the light into the photoresistor, the less resistance it creates, which in turn means there is less voltage to divert to the ADC.

In short, the the darker it is, the more resistance the photoresistor provides and the more voltage is diverted to the ADC.

Here are the specific wiring instructions for the MCP3008 or MCP3208. For the MCP3002 use [this](/images/rpi2/rpi2_lab03_mcp3002_bb.png) diagram.

<img src="/images/rpi2/rpi2_lab03_mcp3008_bb.png"/>

__NOTE:__ _The ADC has a notch out of one side - ensure that the side with the notch is (according to the diagram) on the lower edge of the breadboard._

# Build the Universal Windows Platform Application
In this application you will read the voltage value coming into the ADC from the voltage divider - the higher the voltage, the darker it is (remember, you are reading in the residual voltage, which is diverted to the ADC when there is resistance in the photoresistor). You will use the _darkness_ value to determine if the LED should be on or off.

The ADC is connected to the RPi2 through the Serial Peripheral Interface (SPI) bus. SPI is a synchronous serial communication interface specification used for short distance communication, primarily in embedded systems. SPI devices communicate in full duplex mode using a master-slave architecture with a single master. The master device originates the frame for reading and writing. Multiple slave devices are supported through selection with individual slave select (SS) lines. SPI is a four-wire serial bus as follows:

1. SCLK - Serial Clock (output from master).
2. MOSI - Master Output, Slave Input (output from master).
3. MISO - Master Input, Slave Output (output from slave).
4. SS - Slave Select (active low, output from master).

We won't go any deeper into SPI or the pin layout of the two ADCs - suffice to say that the ADC is wired up to support the four-channel SPI bus, plus supply voltage and ground. The wire connecting the voltage divider to the ADC is the input channel you will read the residual voltage from. 

## Create a Blank Universal App
Launch Visual Studio and start a new __Blank App (Universal Windows)__ (found in the _C# -> Windows -> Universal_ node).

Name the application _IoTLigthSensor_.

## Add the Windows IoT Extensions for the UWP
Once the solution is created, click on the _Project_ menu and select _Add Reference_.

In the Reference Manager dialog, expand the _Universal Windows_ node and select _Extensions_.

In the list of extensions, check the box next to __Windows IoT Extensions for the UWP__ and click __OK__.

## Add the Microsoft.Azure.Devices.Client NuGet Package
Once the _Windows IoT Extensions for the UWP_ are added, click on the _Project_ menu and select _Manage NuGet Packages.._

Use the search field to search for __Microsoft.Azure.Devices.Client__. 

__NOTE:__ You must check the _Include prereleases_ box.

Click on the __Install__ button to install the package.

<img src="/images/rpi2/rpi2_lab03_AzureNuGet.PNG"/>

## Design the App UI
Open the _MainPage.xaml_ file. This is the layout definition for the initial page that loads when the app is run. Next you will add a few elements to the page.

{% highlight xml %}
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <StackPanel HorizontalAlignment="Center" VerticalAlignment="Center">
        <TextBlock x:Name="StatusText" Text="Waiting for initialization" Margin="10,30,10,10" TextAlignment="Center" FontSize="26" />
        <TextBlock x:Name="textPlaceHolder" Text="N/A" Margin="10,50,10,10" TextAlignment="Center" FontSize="26" />
        <Rectangle x:Name="IndicatorBar" Height="20" Fill="LightGray" Width="300"/>
        <ScrollViewer>
            <TextBlock x:Name="MessageLog" Text="Message Log" Margin="10,50,10,10" TextAlignment="Center" Height="150" FontSize="16" />
        </ScrollViewer>
    </StackPanel>
</Grid>
{% endhighlight %}

## Add 'using' Statements
Open the _MainPage.xaml.cs_ file. This is the code behind the layout for the MainPage.xaml. Add the following to the _using_ statements at the top of the file. Add the following <code>using</code> 

{% highlight csharp %}
using Windows.Devices.Gpio;
using Windows.Devices.Spi;
using Windows.Devices.Enumeration;
using System.Threading;
using System.Threading.Tasks;
using System.Text;
using Microsoft.Azure.Devices.Client;
{% endhighlight %}

## Define Constants and Variables
There are several constants and variables that you will reference throughout this code. This code is written to support the MCP3002, MCP3008, and MCP3208 ADCs. You must set the value of <code>ADC_DEVICE</code> to the specific ADC you are using, and follow the appropriate wiring diagram (above). 

{% highlight csharp %}
public sealed partial class MainPage : Page
{
    /* Important! Change this to either AdcDevice.MCP3002, AdcDevice.MCP3208 or AdcDevice.MCP3008 depending on which ADC you chose     */ 
    private AdcDevice ADC_DEVICE = AdcDevice.MCP3008;

    enum AdcDevice { NONE, MCP3002, MCP3208, MCP3008 };
    
    // Use the device specific connection string here
    private const string IOT_HUB_CONN_STRING = "YOUR DEVICE SPECIFIC CONNECTION STRING GOES HERE";
    // Use the name of your Azure IoT device here - this should be the same as the name in the connections string
    private const string IOT_HUB_DEVICE = "YOUR DEVICE NAME GOES HERE";
    // Provide a short description of the location of the device, such as 'Home Office' or 'Garage'
    private const string IOT_HUB_DEVICE_LOCATION = "YOUR DEVICE LOCATION GOES HERE";
    
    // Line 0 maps to physical pin 24 on the RPi2
    private const Int32 SPI_CHIP_SELECT_LINE = 0; 
    private const string SPI_CONTROLLER_NAME = "SPI0";

    // 01101000 channel configuration data for the MCP3002
    private const byte MCP3002_CONFIG = 0x68; 
    // 00000110 channel configuration data for the MCP3208
    private const byte MCP3208_CONFIG = 0x06; 
    // 00001000 channel configuration data for the MCP3008
    private const byte MCP3008_CONFIG = 0x08; 

    private const int RED_LED_PIN = 12;

    private SolidColorBrush redFill = new SolidColorBrush(Windows.UI.Colors.Red);
    private SolidColorBrush grayFill = new SolidColorBrush(Windows.UI.Colors.LightGray);

    private DeviceClient deviceClient;
    private GpioPin redLedPin;
    private SpiDevice spiAdc;
    private int adcResolution;
    private int adcValue;
    
    private Timer readSensorTimer;
    private Timer sendMessageTimer;

    public MainPage()
    {
        this.InitializeComponent();
    }
}
{% endhighlight %}

There is a lot defined here - the function of each constant or variable will become evident as you code up the application.

## Add a Clean Up Event Handler
Inside the _MainPage()_ constructor, register an event handler for the __Unloaded__ event. 

{% highlight csharp %}
public MainPage()
{
    this.InitializeComponent();
    
    // Register the Unloaded event to clean up on exit
    Unloaded += MainPage_Unloaded;
}
{% endhighlight %}

This event handler will be invoked whenever the _MainPage_ is unloaded. You will use it to clean up a few resources. Use the Visual Studio Lightbulb feature to add the __MainPage_Unloaded__ event handler and add the following code to dispose of connections to the pins on the RPi2.

{% highlight csharp %}
private void MainPage_Unloaded(object sender, RoutedEventArgs e)
{
    if (spiAdc != null)
    {
        spiAdc.Dispose();
    }
    
    if (redLedPin != null)
    {
        redLedPin.Dispose();
    }
}
{% endhighlight %}

## Initialize the SPI and GPIO Busses
Still in the _ManPage()_ constructor, add a call to a new method names __InitAll()__. 

{% highlight csharp %}
public MainPage()
{
    this.InitializeComponent();
    
    // Register the Unloaded event to clean up on exit
    Unloaded += MainPage_Unloaded;
    
    // Initialize GPIO and SPI
    InitAll();
}
{% endhighlight %}

Use the Visual Studio Lightbulb feature to add the __InitAll()__ method. Modify the method signature to mark it as an asyn method.

{% highlight csharp %}
private async void InitAll()
{
    try
    {
        InitGpio();
        await InitSpiAsync();
    }
    catch (Exception ex)
    {
        StatusText.Text = ex.Message;
        return;
    }
    
    // TODO: Read sensors every 100ms and refresh the UI
    // TODO: Instantiate the Azure device client
    // TODO: Send messages to Azure IoT Hub every one-second
    
    StatusText.Text = "Status: Running";
}
{% endhighlight %}

This method invokes two additional methods to initialize the GPIO and SPI busses. Use the Visual Studio Lightbulb feature to create the __InitGpio()__ method. Initialize the GPIO by assigning the default GPIO controller to the <code>gpio</code> variable, and check for _null_ (throw an exception if it is _null_). Next, initialize the <code>redLedPin</code> in the same way you did in [Lab 01](../01/).

{% highlight csharp %}
private void InitGpio()
{
    var gpio = GpioController.GetDefault();

    if (gpio == null)
    {
        throw new Exception("There is no GPIO controller on this device.");
    }
    
    redLedPin = gpio.OpenPin(RED_LED_PIN);
    redLedPin.Write(GpioPinValue.High);
    redLedPin.SetDriveMode(GpioPinDriveMode.Output);
}
{% endhighlight %}

Go back to the _MainPage()_ constructor and use the Visual Studio Lightbulb feature to add the __InitSpi()__ method. In this method you will initialize the SPI buss so that you can use it to communicate through the ADC. Modify the method signature to mark it as an async method.

{% highlight csharp %}
private async Task InitSpiAsync()
{
    try
    {
        var settings = new SpiConnectionSettings(SPI_CHIP_SELECT_LINE);
        // 3.6MHz is the rated speed of the MCP3008 at 5v
        settings.ClockFrequency = 3600000;
        // The ADC expects idle-low clock polarity so we use Mode0
        settings.Mode = SpiMode.Mode0; 
        // Get a selector string that will return all SPI controllers on the system
        string spiAqs = SpiDevice.GetDeviceSelector(SPI_CONTROLLER_NAME);
        // Find the SPI bus controller devices with our selector string 
        var deviceInfo = await DeviceInformation.FindAllAsync(spiAqs);
        // Create an SpiDevice with our bus controller and SPI settings
        spiAdc = await SpiDevice.FromIdAsync(deviceInfo[0].Id, settings);
    }
    catch (Exception ex)
    {
        throw new Exception("SPI initialization failed.", ex);
    }
}
{% endhighlight %}

## Create a Timer to Read the Sensor Values
Next you will create a timer to read the data from the photoresistor and set the state of the LED. To do this, in the _MainPage()_ constructor, replace <code>// TODO: Read sensors every 25ms and refresh the UI</code> with:

{% highlight csharp %}
// Read sensors every 100ms and refresh the UI
readSensorTimer = new Timer(this.SensorTimer_Tick, null, 0, 100);
{% endhighlight %}

Use the Visual Studio Lightbulb feature to add an event handler for __SensorTimer\_Tick__.

{% highlight csharp %}
private void SensorTimer_Tick(object state)
{
    ReadAdc();
    LightLed();
}
{% endhighlight %}

In _SensorTmer\_Tick_ you will call two methods - one to read the sensor data in, and one to set the state of the LED. Use the Visual Studio Lightbulb feature to create the __ReadAdc()__ method.

{% highlight csharp %}
private void ReadAdc()
{
    // Create a buffer to hold the read data
    byte[] readBuffer = new byte[3];
    byte[] writeBuffer = new byte[3] { 0x00, 0x00, 0x00 };

    switch (ADC_DEVICE)
    {
    case AdcDevice.MCP3002:
        writeBuffer[0] = MCP3002_CONFIG;
        break;
    case AdcDevice.MCP3008:
        writeBuffer[0] = MCP3008_CONFIG;
        break;
    case AdcDevice.MCP3208:
        writeBuffer[0] = MCP3208_CONFIG;
        break;
    }

    // Read data from the ADC
    spiAdc.TransferFullDuplex(writeBuffer, readBuffer);
    adcValue = convertToInt(readBuffer);

    // UI updates must be invoked on the UI thread
    var task = this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
    {
        textPlaceHolder.Text = adcValue.ToString();
        IndicatorBar.Width = Map(adcValue, 0, adcResolution - 1, 0, 300);
    });
}
{% endhighlight %}

In this method you create a command buffer to write to the ADC, and a read buffer to capture the values from the ADC. The SPI configuration (based on which ADC you are using) is the first node in the command/write buffer. When you call <code>TransferFullDuplex()</code> you open a two-way channel with the ADC over the SPI bus - a command/write channel and a read channel. 

The <code>convertToInt(readBuffer)</code> is used to convert the byte array returned from the ADC into an integer. Use the Visual Studio Lightbulb feature to add __convertToInt(byte[])__. Modify the method signature - change the input variable name from _readBuffer_ to __data__. Each ADC returns the data a little differently, so this command will convert the byte array to an integer based on the ADC you are using.

{% highlight csharp %}
private int convertToInt(byte[] data)
{
    int result = 0;
    switch (ADC_DEVICE)
    {
        case AdcDevice.MCP3002:
            result = data[0] & 0x03;
            result <<= 8;
            result += data[1];
            break;
        case AdcDevice.MCP3008:
            result = data[1] & 0x03;
            result <<= 8;
            result += data[2];
            break;
        case AdcDevice.MCP3208:
            result = data[1] & 0x0F;
            result <<= 8;
            result += data[2];
            break;
    }
    return result;
}
{% endhighlight %}

In the _ReadAdc()_ method there was also a reference to a _Map()_ method. Use the Visual Studio Lightbulb feature to add Map(int, int, int, int, int)__. This method makes it easy to map data from one value range to another. Modify the method signature according to the code example below.

{% highlight csharp %}
private double Map(int val, int inMin, int inMax, int outMin, int outMax)
{
    return Math.Round((double)((val - inMin) * (outMax - outMin) / (inMax - inMin) + outMin));
}
{% endhighlight %}

In this example you are using the _Map()_ method to map the value from the ADC, which is a range 0 - 1023 (or 0-4096 for the 12-bit MCP3208), to a range of 0 - 300, which is used to define the width of the darkness indicator bar in the UI (max width is 300).

Next, return to the _SensorTimer\_Tick_ method and use the Visual Studio Lightbulb feature to add an event handler for __LightLed()__.

{% highlight csharp %}
private void LightLed()
{
    SolidColorBrush fillColor = grayFill;

    // Turn on LED if potentiometer is rotated more than halfway
    switch (ADC_DEVICE)
    {
        case AdcDevice.MCP3208:
            adcResolution = 4096;
            break;
        case AdcDevice.MCP3008:
            adcResolution = 1024;
            break;
        case AdcDevice.MCP3002:
            adcResolution = 1024;
            break;
    }

    if (adcValue > adcResolution * 0.66)
    {
        redLedPin.Write(GpioPinValue.High);
        fillColor = redFill;
    }
    else
    {
        redLedPin.Write(GpioPinValue.Low);
        fillColor = grayFill;
    }

    // UI updates must be invoked on the UI thread
    var task = this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
    {
        IndicatorBar.Fill = fillColor;
    });
}
{% endhighlight %}

In this method you simply check to see if the ADC value is greater than two-thirds of the ADC's resolution - in other words, is it kind of dark? If it is, turn on the LED and paint the indicator bar in the UI red, otherwise, turn off the LED and paint the indicator bar light gray.

## Test Run the App
At this point you can deploy and run the application to see if the photoresistor and LED are working. You still haven't sent a message to Azure, but testing the circuit is never a bad idea. To deploy this application to your RPi2, select __ARM__ from the _Solution Platforms_ list in the toolbar, and select __REMOTE MACHINE__ from the _Device_ dropdown list in the toolbar.

<img src="/images/rpi2/rpi2_lab01_arm.png"/>

You will be prompted with the _Remote Connections_ dialog. Select your device from the list of _Auto Detected_ devices, or type in the device name or IP address into the _Manual Configuration_ textbox (set the _Authentication Mode_ to __None__) and click _Select_.

<img src="/images/rpi2/rpi2_lab01_remote.png"/>

__NOTE:__ You can verify or modify these values by navigating to the project properties (select Properties in the Solution Explorer) and choosing the Debug tab on the left.

Now press __F5__ to run the application and you should see it deploy on the RPi2. Test the circuit and application by changing the amount of light the photoresistor is exposed to.

## Send A Message to Azure IoT Hub
Now that you know your physical device is working, it is time to send its data to Azure. In the _MainPage()_ constructor, replace the comment <code>// TODO: Instantiate the Azure device client</code> with:

{% highlight csharp %}
// Instantiate the Azure device client
deviceClient = DeviceClient.CreateFromConnectionString(IOT_HUB_CONN_STRING);
{% endhighlight %}

Next, replace the comment<code>// TODO: Send messages to Azure IoT Hub every one-second</code> with:

{% highlight csharp %}
// Send messages to Azure IoT Hub every one-second
sendMessageTimer = new Timer(this.MessageTimer_Tick, null, 0, 1000);
{% endhighlight %}

Use the Visual Studio Lightbulb feature to create the __MessageTimer\_Tick()__ event handler.

{% highlight csharp %}
private void MessageTimer_Tick(object state)
{
    SendMessageToIoTHubAsync(adcValue);
}
{% endhighlight %}

Each tme the _MessageTimer_ ticks (once per second) this event handler will invoke the _SendMessageToIoTHubAsync()_ method. Use the Visual Studio Lightbulb feature to create the __SendMessageToIoTHubAsync()__ method. Modify the method signature to mark it as an async method.

{% highlight csharp %}
private async Task SendMessageToIoTHubAsync(int darkness)
{
    try
    {
        var payload = "{\"deviceId\": \"" +
            IOT_HUB_DEVICE + 
            "\", \"location\": \"" +
            IOT_HUB_DEVICE_LOCATION +
            "\", \"data\":" +
            darkness + 
            "\", \"localTimestamp\": \"" +
            DateTime.Now.ToLocalTime().ToString() + 
            "\"}";

        // UI updates must be invoked on the UI thread
        var task = this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            MessageLog.Text = "Sending message: " + payload + "\n" + MessageLog.Text;
        });

        var msg = new Message(Encoding.UTF8.GetBytes(payload));

        await deviceClient.SendEventAsync(msg);
    }
    catch (Exception ex)
    {
        // UI updates must be invoked on the UI thread
        var task = this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            MessageLog.Text = "Sending message: " + ex.Message + "\n" + MessageLog.Text;
        });
    }
}
{% endhighlight %}

With this method you attempt to construct a JSON message payload, display it on the screen and send it to your Azure IoT Hub. The communication with the Azure IoT Hub is managed by the <code>deviceClient</code> object from the _Microsoft.Azure.Devices.Client_ namespace.

The final _MainPage.cxaml.cs_ can be founs [here](https://github.com/ThingLabsIo/IoTLabs/blob/master/RPi2/Lab03/Lab03/MainPage.xaml.cs) and the complete solution can be found [here](https://github.com/ThingLabsIo/IoTLabs/tree/master/RPi2/Lab03).

## Run the Application
Now you can run the application on your RPi2 and not only will you see the indicator bar changing, but you will also see the log of messages being sent to Azure IoT Hub at a rate of once per second.

# Conclusion &amp; Next Steps
Congratulations! You have built a Universal Windows Platform application that captures data from the physical word and sends it to Azure IoT Hub. The core concepts you've learned are:

1. Working with analog data using a voltage divider and an analog-t-digital converter (ADC). 
2. Configuring and using the Serial Peripheral Interface (SPI).
3. Creating and sending messages to Azure IoT Hub. 

At this point, nothing interesting is happening with that data you are sending to Azure. It is simply being persisted for a default amount of time (1-day) and then being dropped. In the [next lab][nextlab] you will setup some Azure services to process and visualize the data.

<a class="radius button small" href="{{ site.url }}/lang/cs/visualize-iot-with-powerbi/">Go to 'Visualize IoT Data with Microsoft Power BI' ›</a>

[nextlab]: ../visualize-iot-with-powerbi/