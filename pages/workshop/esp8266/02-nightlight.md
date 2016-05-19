---
layout: "page-fullwidth"
title: "Nightlight"
subheadline: "Building Connected Things with an ESP8266 and Microsoft Azure"
teaser: "In this lab, you will create a device that responds to ambient light and changes the intensity of an LED - a nightlight."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [esp8266, lua, MQTT, iot, maker]
permalink: /workshop/esp8266/nightlight/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab, you will create a simple _Thing_, using Lua on your ESP8266, that changes the intensity of a LED in response to ambient light measured with a photocell. Your own nightlight! 

# Bill of Materials
In this lab series you will need the following:

1. [Microsoft Azure IoT Starter Kit w/ Adafruit Feather HUZZAH](https://www.adafruit.com/product/3032), including
    - Photocell
    - 1 - 10 KOhm Resistor
    
# Overview
In this lab, you will combine the output device you previously made (The LED) with an input sensor (a Photocell). You will use the measurement of ambient light from the Photocell to control the intensity of the LED. You are making a nightlight. 

A _photocell_, also known as a _light-dependent resistor (LDR)_ or a _photoresistor_, works by limiting the amount of voltage that passes through it based on the intensity of light detected. The resistance decreases as light input increases - in other words, the more light, the more voltage passes through the photoresistor. By measuring how much voltage is passed through the photoresistor you can determine the range of ambient light, using a measurement of 0-1023.

In this lab, you will measure the amount of ambient light and increase the brightness intensity of an LED at an inverse rate to the amount of light (i.e. as it gets darker the LED gets brighter). In the previous lab, you connected the LED sensor to digital pin 4, which only supports On/Off states. There is a technique for using a digital device (like an LED) to simulate analog behavior, such as fading the brightness of an LED over a range of 256 value stops (0-255) instead of simply 0 or 1 - _Pulse Width Modulation_.

# Pulse Width Modulation (PWM)
Pulse Width Modulation (PWM) is a technique for simulating analog values on a digital actuator. PWM simulates analog data by creating a square wave (basically a repeating switch between on and off) where the duration of 'on' time is the pulse width. If the square wave has a 50% pulse width (more commonly known as a duty cycle), then the output from that pin is equal amounts on and off. If the duty cycle is 25% then the output from the pin will be on for only one-quarter of the duty cycle (inversely it will be off for three times as long as it is on - 25% on, 75% off).

![PWM Duty Cycle]({{site.url}}/images/duty-cycle.jpg)

Because the time window of a cycle is too fast for the human eye to perceive (about 2 milliseconds for this example), instead of causing an LED to strobe or flicker, it simply appears to be more or less bright. Using a 25% duty cycle the LED would be __On__ for half a millisecond and __Off__ for 1.5 milliseconds, which makes the LED appear to be at 25% brightness. So while we aren't truly sending analog data to a digital LED, we are using PWM to simulate the effect of analog data.

NodeMCU (our firmware) only supports _Pulse Width Modulation (PWM)_ on a maximum of 6 pins.

# Connecting the Sensors
Wire up the components as shown here:

<img src="/images/ESP8266-Photocell-Wiring.jpeg" alt="Wire up the photocell" style="width: 400px;"/>

* Connect the Photocell to ADC and Ground
* Connect the Resistor to +3v and ADC

# Create the Lua Program in ESPlorer 

As in the previous lab, you'll write lua code in ESplorer. This code (below), operates the photocell and LED as a nightlight.

1. Launch ESPlorer.jar, select your serial port, and press the 'Open' button

<img src="/images/esplorer-connect.png" alt="Launch Esplorer, connect your device" style="width: 400px;"/>

2. Create a new script in Esplorer with these contents. Save it as Lab02.lua

{% highlight lua %}
--
-- LAB 02: NightLight
--
-- This program blinks an LED, it is designed for the Huzzah Feather
-- ESP8266 module.
--

-- Constants
MAXLIGHT = 1024

-- Symbolic names for the pins
REDLED   = 1
GREENLED = 2
BLUELED  = 5

-- There is only one Analog Channel
PHOTOCELL= 0

-- Set the GPIO pins controlling the LED to PWM mode.
-- There are three pins, one each for: Red, Green, and Blue.
pwm.setup(REDLED, 500, 512)
pwm.setup(GREENLED, 500, 512)
pwm.setup(BLUELED, 500, 512)
pwm.start(REDLED)
pwm.start(GREENLED)
pwm.start(BLUELED)

function led(r, g, b)
    pwm.setduty(REDLED, r)
    pwm.setduty(GREENLED, g)
    pwm.setduty(BLUELED, b)
end
led(512, 0, 0) --  set led to red
led(0, 0, 512) -- set led to blue.

-- This is the flash_led function
function check_light()
    -- Read the light level from the photocell
    -- We invert it by subtracting from the maximum value
    light = MAXLIGHT - adc.read(PHOTOCELL)

    print("Detected Light Level: "..light)

    -- Set the LED Brightness
    led(light, light, light)
end

-- Creates a timer 
--   Timer id = 1
--   Duration = 100 microseconds
--   Mode     = tmr.ALARM_AUTO (reregister to do it again forever)
--   Callback = flash_led
tmr.alarm(1, 100, tmr.ALARM_AUTO, check_light)
{% endhighlight %}

# Run the App on the Device
To run the application you will save it to the ESP8266, reset the device, then invoke the code.

1. Press the button 'Save to ESP' on the lower left of the ESPlorer interface.
2. Push the reset button on the ES8266
3. When it's done booting, click the 'Reload' button on the right side.
   You should see a list of files on the ESP8266
4. Double click the file on the right hand side that you saved.
   This should execute your code.

# Conclusion &amp; Next Steps
Congratulations! You have created a _Thing_ that uses an input sensor to measure ambient light to control the output to an LED. The concepts you learned in this lab are:

1. Input sensors vs. output actuators.
2. Pulse Width Modulation (PWM), which is a technique for simulating analog values on a digital actuator by turning the actuator on and off for portions of the duty cycle.

In the [next lab][nextlab], you will extend this device to build the __ThingLabs Weather Station__ - a multi-sensor, device you will use for the remainder of this workshop.

<a class="radius button small" href="{{ site.url }}/workshop/esp8266/weather/">Go to 'ThingLabs Weather Station' ›</a>

[nextlab]: /workshop/esp8266/weather/