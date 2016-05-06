---
layout: "page-fullwidth"
title: "Hello, Lua IoT!"
subheadline: "Building Connected Things with an ESP8266 and Microsoft Azure"
teaser: "In this lab, you will create a simple 'Thing' using Lua on the ESP8266."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [esp8266, lua, MQTT, iot, maker]
permalink: /workshop/esp8266/hello-lua-iot/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab, you will create a simple _Thing_ using a ESP8266 and the Lua programming language. 

# Bill of Materials
In this lab series you will need the following:

1. [Microsoft Azure IoT Starter Kit w/ Adafruit Feather HUZZAH](https://www.adafruit.com/product/3032), including
    1. RGB LED
    2. 3 - XXX Resistors

If you haven't already done so, follow the setup instructions at ['Getting Started']({{ site.url }}/workshop/esp8266/getting-started/).

# Assemble the Parts
Insert the Huzzah Feather carefully into the prototyping board

![Assembled Parts](/images/huzzah-feather-board.png)

The Huzzah Feather connects to the physical world through the a set of pins. These pins have various functions, but most are General Purpose Input/Output (GPIO) pins. 
Because there are so few connections to the Huzzah, some of the pins are used for more than one function and 
need to be configured before you can use them. In fact, GPIO pins can be used for both input and output and 
therefore need configuration to tell them how to operate.
The inputs can be from switches, sensors or other devices. The outputs can be LEDs, servos, motors or countless other devices. 
Besides the GPIO Pins there are a set of other pins including:

**#0, #2, #4, #5, #12, #13, #14, #15, #16** - GPIO

**GND** - this is the common ground for all power and logic

**BAT** - this is the positive voltage to/from the JST jack for the optional Lipoly battery

**USB** - this is the positive voltage to/from the micro USB jack if connected

**EN** - this is the 3.3V regulator's enable pin. It's pulled up, so connect to ground to disable the 3.3V regulator

**3V** - this is the output from the 3.3V regulator, it can supply 500mA peak (try to keep your current draw under 250mA so you have plenty for the ESP8266's power 

**RST** - this is the reset pin for the ESP8266, pulled high by default. When pulled down to ground momentarily it will reset the ESP8266 system. This pin is 3.3V logic only

**EN (CH_PD)** - This is the enable pin for the ESP8266, pulled high by default. When pulled down to ground momentarily it will reset the ESP8266 system. This pin is 3.3V logic only

![Huzzah Layout](/images/huzzah-layout.png)

**I2C SDA** = GPIO #4 (default) 

**I2C SCL** = GPIO #5 (default)

**SPI SCK** = GPIO #14 (default) 

**SPI MOSI** = GPIO #13 (default) 

**SPI MISO** = GPIO #12 (default)

**ADC** - Analog to Digital Converter Pin, 1.0V max.

Serial Communication Occurs over TX/RX:

**TX** - output from the module and is 3.3V logic.

**RX** - input into the module and is 5V compliant (there is a level shifter on this pin)

Connect the LED to the D4 jack.

![Connect the LED to D4](/images/workshops/thingy-4-windows/blink_example.jpg)

# Create the Lua Program in ESPlorer 
The Lua programming language is a simple language designed to avoid the clutter of many languages. It's powerful _enough_ and simple _enough_ to make it an effective language for working on devices. It's also much more user friendly than other languages typically used in small devices, like _forth_, and more flexible than _C_.

To write your first lua program for the ESP8266 you'll only have to write a few lines of code.

1. Launch ESPlorer.jar, select your serial port, and press the 'Open' button

![Launch Esplorer, connect your device](/images/esplorer-connect.png)

2. Cut and Paste or write this code into the left side of the ESPlorer

{% highlight lua %}
--
-- LAB 01: Hello, Lua IoT!
--
-- This program blinks an LED, it is designed for the Huzzah Feather ESP8266 module.
--

-- Symbolic names for the pins
REDLED   = 1
GREENLED = 2
BLUELED  = 5

-- Number of milliseconds to leave the LED on
STAYLIT  = 250

-- Set the GPIO pins controlling the LED to output mode.
-- There are three pins, one each for: Red, Green, and Blue.
gpio.mode(REDLED,   gpio.OUTPUT)
gpio.mode(GREENLED, gpio.OUTPUT)
gpio.mode(BLUELED,  gpio.OUTPUT)

-- The flash_led function blinks the Green LED
function flash_led()
    -- LOW turns the LED on
    gpio.write(BLUELED, gpio.LOW)
    -- We leave the LED on for 1/4 second
    tmr.delay(STAYLIT)       
    -- HIGH turns the LED off
    gpio.write(BLUELED, gpio.HIGH)
end

-- Creates a timer 
--   Timer id = 1
--   Duration = 1000 microseconds (1 second)
--   Mode     = tmr.ALARM_AUTO (reregister to do it again forever)
--   Callback = flash_led
tmr.alarm(1, 1000, tmr.ALARM_AUTO, flash_led)
{% endhighlight %}

That's all there is to it. Now you are ready to run the application. 

# Run the App on a Device
To run the application you will save it to the ESP8266, reset the device, then invoke the code.

1. Press the button 'Save to ESP' on the lower left of the ESPlorer interface.
2. Push the reset button on the ES8266
3. When it's done booting, click the 'Reload' button on the right side.
   You should see a list of files on the ESP8266
4. Double click the file on the right hand side that you saved.
   This should execute your code.

# Conclusion &amp; Next Steps
Congratulations! You have built a Windows 10 IoT application that controlled a device connected to a Raspberry Pi 2. The core concepts you've learned are:

1. Building a Windows 10 IoT Background Task application that can run on a Windows 10 IoT device. 
2. Using _BackgroundTaskDeferral_ to enable an application to run even after the ```Run()``` method completes.
3. Controlling the state of a device. 

In the [next lab][nextlab], you will build a more complicated _Thing_ that uses both input sensors and output devices. 

<a class="radius button small" href="{{ site.url }}/workshop/thingy-4-windows/nightlight/">Go to 'Nightlight' ›</a>

[nextlab]: /workshop/thingy-4-windows/nightlight/