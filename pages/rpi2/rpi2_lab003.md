---
layout: page-fullwidth
title: "Sending Data to the Cloud"
subheadline: "Raspberry Pi IoT Lab 3"
teaser: "In this lab you will build a Universal Windows Platform application that collects ambient light data and sends it to the Cloud."
show_meta: true
comments: true
header: no
breadcrumb: true
categories:
    - rpi2
author: "Doug Seven"
permalink: /rpi2/03/
---

### Table of Contents
*  Auto generated table of contents
{:toc}

If you haven't already done so, please follow the instructions in [Lab 00: Getting Started](/rpi2/00/) section.

In this lab you will build a Universal Windows Platform application that detects ambient light and sends the data that is being collected to Azure IoT Hub. In following lab you will build a data pipeline to process the incoming data stream and output it to a visualization tool.

## Bill of Materials
What you will need:

1. [Raspberry Pi 2 - $42.00](http://www.amazon.com/Raspberry-Pi-Model-Project-Board/dp/B00T2U7R7I/)
2. [5V 2A Switching Power Supply w/ 20AWG 6' MicroUSB Cable - $7.95](https://www.adafruit.com/product/1995)
3. [(2) Jumper wires (Male to Female) - $1.95](https://www.adafruit.com/product/1954)
4. [(1) Red LED - 25 for $8.00](http://www.adafruit.com/products/297)
5. [(1) 330 Ohm resistors - 100 for $3.90](http://www.amazon.com/E-Projects-Resistors-Watt-330R-Pieces/dp/B00BVOR6IS/)
6. 8GB micro SD card - class 10 or better. Microsoft suggests [this one](http://www.amazon.com/gp/product/B00IVPU786) or [this one](http://www.amazon.com/SanDisk-Ultra-Micro-SDHC-16GB/dp/9966573445).
7. Micro SD card writer


## Wire Up the RPi2
Wire up the RPi2 according to this diagram.

<img src="/images/rpi2/rpi2_lab03_bb.png"/>


This is a dump of all of the code while I write the lab. This is intended for the folks testign this lab as I write it.

{% highlight csharp %}
using System;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Media;

using Windows.Devices.Gpio;
using Windows.Devices.Spi;
using Windows.Devices.Enumeration;
using System.Threading;
using System.Threading.Tasks;
using System.Text;
using Microsoft.Azure.Devices.Client;

namespace Lab03_photoresistor
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        enum AdcDevice { None, MCP3002, MCP3208 };
        
        // Use the device specific connection string here
        private const string IOT_HUB_CONN_STRING = "YOUR DEVICE SPECIFIC CONNECTION STRING GOES HERE";
        // Set this to the appropriate ADC
        private AdcDevice ADC_DEVICE = AdcDevice.MCP3002;

        private const Int32 SPI_CHIP_SELECT_LINE = 0; // Line 0 maps to physical pin 24 on the RPi2
        private const string SPI_CONTROLLER_NAME = "SPI0";
        private const byte MCP3002_CONFIG = 0x68; // 01101000 channel configuration data for MCP3002
        private const byte MCP3208_CONFIG = 0x06; // 00000110 channel configuration data for MCP3208
        private const int RED_LED_PIN = 4;
        private const int WHITE_LED_PIN = 5;

        private SolidColorBrush redFill = new SolidColorBrush(Windows.UI.Colors.Red);
        private SolidColorBrush grayFill = new SolidColorBrush(Windows.UI.Colors.LightGray);

        DeviceClient deviceClient;
        private GpioPin redLedPin;
        private GpioPin whiteLedPin;
        private SpiDevice SpiAdc;
        private Timer readSensorTimer;
        private Timer sendMessageTimer;
        private int adcValue;

        public MainPage()
        {
            this.InitializeComponent();
            // Register the Unloaded event to clean up on exit
            Unloaded += MainPage_Unloaded;
            // Initialize GPIO and SPI
            InitAll();
        }

        private async void InitAll()
        {
            if (ADC_DEVICE == AdcDevice.None)
            {
                StatusText.Text = "Please change the ADC_DEVICE variable to either MPC3002 or MPC3208.";
                return;
            }

            try
            {
                InitGpio();
                await InitSpi();
            }
            catch (Exception ex)
            {
                StatusText.Text = ex.Message;
                return;
            }

            deviceClient = DeviceClient.CreateFromConnectionString(IOT_HUB_CONN_STRING);
            // Read sensors ever 25ms and refresh the UI
            readSensorTimer = new Timer(this.SensorTimer_Tick, null, 0, 25);
            // Send messages to Azure IoT Hub every one-second
            sendMessageTimer = new Timer(this.MessageTimer_Tick, null, 0, 1000);
            // Initiate a check for incoming Cloud-to-Device messages
            ReceiveDataFromAzure();

            StatusText.Text = "Status: Running";
        }

        private void MessageTimer_Tick(object state)
        {
            SendMessageToIoTHub(adcValue);
        }

        private async Task SendMessageToIoTHub(int darkness)
        {
            try {
                var text = "{\"deviceId\": \"Thinglabs00\", \"location\": \"Home Office\", \"data\": \"darkness:" +
                    darkness + "\", \"localTimestamp\": \"" + 
                    DateTime.Now.ToLocalTime().ToString() +"\"}";


                // UI updates must be invoked on the UI thread
                var task = this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    MessageLog.Text = "Sending message: " + text + "\n" + MessageLog.Text;
                });

                var msg = new Message(Encoding.UTF8.GetBytes(text));

                await deviceClient.SendEventAsync(msg);
            }
            catch(Exception ex)
            {
                // UI updates must be invoked on the UI thread
                var task = this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    MessageLog.Text = "Sending message: " + ex.Message + "\n" + MessageLog.Text;
                });
            }
        }

        private void SensorTimer_Tick(object state)
        {
            ReadAdc();
            LightLed();
        }

        private void LightLed()
        {
            int adcResolution = 0;
            SolidColorBrush fillColor = grayFill;

            switch (ADC_DEVICE)
            {
                case AdcDevice.MCP3002:
                    adcResolution = 1024;
                    break;
                case AdcDevice.MCP3208:
                    adcResolution = 4096;
                    break;
            }

            // Tunr on LED if potentiometer is rotated more than halfway
            if (adcValue > adcResolution / 2)
            {
                redLedPin.Write(GpioPinValue.Low);
                fillColor = redFill;
            }
            else
            {
                redLedPin.Write(GpioPinValue.High);
                fillColor = grayFill;
            }

            // UI updates must be invoked on the UI thread
            var task = this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
            {
                IndicatorBar.Fill = fillColor;
            });
        }

        private void ReadAdc()
        {
            byte[] readBuffer = new byte[3]; // Buffer to hold read data
            byte[] writeBuffer = new byte[3] { 0x00, 0x00, 0x00 };

            switch (ADC_DEVICE)
            {
                case AdcDevice.MCP3002:
                    writeBuffer[0] = MCP3002_CONFIG;
                    break;
                case AdcDevice.MCP3208:
                    writeBuffer[0] = MCP3208_CONFIG;
                    break;
            }

            SpiAdc.TransferFullDuplex(writeBuffer, readBuffer); // Read data from ADC
            adcValue = convertToInt(readBuffer);

            // UI updates must be invoked on the UI thread
            var task = this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
            {
                textPlaceHolder.Text = adcValue.ToString();
                IndicatorBar.Width = Map(adcValue, 0, 1023, 0, 300);
            });
        }

        private double Map(int val, int inMin, int inMax, int outMin, int outMax)
        {
            return Math.Round((double)((val - inMin) * (outMax - outMin) / (inMax - inMin) + outMin));
        }

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
                case AdcDevice.MCP3208:
                    result = data[1] & 0x0F;
                    result <<= 8;
                    result += data[2];
                    break;
            }

            return result;
        }

        private async Task InitSpi()
        {
            try
            {
                var settings = new SpiConnectionSettings(SPI_CHIP_SELECT_LINE);
                settings.ClockFrequency = 500000; // 0.5 MHz clock rate
                settings.Mode = SpiMode.Mode0; // The ADC expects idle-low clock polarity so we use Mode0

                string spiAqs = SpiDevice.GetDeviceSelector(SPI_CONTROLLER_NAME);
                var deviceInfo = await DeviceInformation.FindAllAsync(spiAqs);
                SpiAdc = await SpiDevice.FromIdAsync(deviceInfo[0].Id, settings);

            }
            catch (Exception ex)
            {
                throw new Exception("SPI initialization failed.", ex);
            }
        }

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

        private void MainPage_Unloaded(object sender, RoutedEventArgs e)
        {
            if (SpiAdc != null)
            {
                SpiAdc.Dispose();
            }

            if (redLedPin != null)
            {
                redLedPin.Dispose();
            }

        }
    }
}
{% endhighlight %}