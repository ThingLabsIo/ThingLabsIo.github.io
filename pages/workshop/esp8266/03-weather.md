---
layout: "page-fullwidth"
title: "Weather Station"
subheadline: "Building Connected Things with an ESP8266 and Microsoft Azure"
teaser: "In this lab, you will create a weather station that responds to records light, temperature and humidity and changes the intensity of an LED."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [esp8266, lua, MQTT, iot, maker]
permalink: /workshop/esp8266/weather/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab, you will create a simple _Thing_ using a Windows 10 IoT device and the Universal Windows Platform. 

# Bill of Materials
In this lab series you will need the following:

1. [Microsoft Azure IoT Starter Kit w/ Adafruit Feather HUZZAH](https://www.adafruit.com/product/3032), including
    - DHT22
        
# Overview
In this lab, you will combine the nightlight you previously built with a DHT22 -- which measures temperature and humidity. You will collect the ambient light, the temperature and humidity and use it as a minimal weather station. This data will get sent to Azure so you can start exploring the data processing pipeline for IoT data. 

# Connecting the Sensors
Connect the DHT22 to the ESP8266 (Huzzah Feather) as shown in these images:

<img src="/images/ESP8266-DHT-Rails.jpeg" alt="Connecting DHT to Rails" style="width: 400px;"/>
<img src="/images/ESP8266-DHT-Signal.jpeg" alt="Connecting DHT to ESP8266" style="width: 400px;"/>

* Connect the DHT22 Left Connection (when facing it) to +3V
* Connect the DHT22 Right Connection (when facing it) to Ground
* Connect the DHT22 Connection 2nd from Left when facing it, to Huzzah Feather Pin 12 (NodeMCU Pin 6)

# Create the Lua Program in ESPlorer 

As in the previous lab, you'll write lua code in ESplorer. This code (below), operates the photocell and LED as a nightlight.

1. Launch ESPlorer.jar, select your serial port, and press the 'Open' button

<img src="/images/esplorer-connect.png" alt="Launch Esplorer, connect your device" style="width: 400px;"/>

2. Create a new script in Esplorer with these contents. Save it as Lab03.lua

{% highlight lua %}
--
-- LAB 03: Weather
--
-- This program collects temperature and humidity from the DT22.
--

-- Constants
MAXLIGHT = 1024

-- Symbolic names for the pins
REDLED   = 1
GREENLED = 2
BLUELED  = 5

REDLEDONBOARD = 3

-- There is only one Analog Channel
PHOTOCELL= 0
DHTPIN   = 6

-- Set the GPIO pins controlling the LED to PWM mode.
-- There are three pins, one each for: Red, Green, and Blue.
pwm.setup(REDLED, 500, 512)
pwm.setup(GREENLED, 500, 512)
pwm.setup(BLUELED, 500, 512)
pwm.start(REDLED)
pwm.start(GREENLED)
pwm.start(BLUELED)

gpio.mode(REDLEDONBOARD, gpio.OUTPUT)

-- Sets the cycle rate (for PWM) of the LEDs
function led(r, g, b)
    pwm.setduty(REDLED, r)
    pwm.setduty(GREENLED, g)
    pwm.setduty(BLUELED, b)
end

-- This is the flash_led function
function check_weather()
    -- Turn the onboard LED ON when we run this routine
    gpio.write(REDLEDONBOARD, gpio.LOW)
    
    light = MAXLIGHT - adc.read(PHOTOCELL)
    --print("Light: "..light)
        
    status, temperature, humidity, temperature_dec, humidity_dec = dht.read(DHTPIN)
    if status == dht.OK then
        -- Integer firmware using this example
        -- print(string.format("DHT Temperature:%d.%03d;Humidity:%d.%03d\r\n",
        --       math.floor(temperature),
        --       temperature_dec,
        --       math.floor(humidity),
        --       humidity_dec
        -- ))
            
        -- Float firmware using this example
        -- print("DHT Temperature: "..temperature.."; ".."Humidity: "..humidity)
    elseif status == dht.ERROR_CHECKSUM then
        print( "DHT Checksum error." )
    elseif status == dht.ERROR_TIMEOUT then
        print( "DHT timed out." )
    end

    print("Light: "..light.." Temperature: "..temperature.." ".." Humidity: "..humidity)


    -- Set the LED Brightness
    led(light, light, light)

    -- Turn the onboard LED OFF when we exit this routine
    gpio.write(REDLEDONBOARD, gpio.HIGH)
end

-- Creates a timer 
--   Timer id = 1
--   Duration = 500 microseconds (1/2 second)
--   Mode     = tmr.ALARM_AUTO (reregister to do it again forever)
--   Callback = flash_led
tmr.alarm(1, 500, tmr.ALARM_AUTO, check_weather)
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
Congratulations! You have created the ThingLabs Weather Station. In the following labs you will use the Weather Station to connect to Azure IoT services and track all of this data. The concepts you learned in this lab are:

1. Working with multiple sensors.
2. Maintaining state while constantly polling sensors for state changes.

In the [next lab][nextlab] you will set up an Azure IoT Hub to use with the ThingLabs Weather Station. 

<a class="radius button small" href="{{ site.url }}/workshop/esp8266/setup-azure-iot-hub/">Go to 'Setting Up Azure IoT' ›</a>

[nextlab]: /workshop/esp8266/setup-azure-iot-hub/