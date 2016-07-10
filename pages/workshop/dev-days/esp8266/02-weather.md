---
layout: "page-fullwidth"
title: "Weather Station"
subheadline: "Building Connected Things with an ESP8266 and Microsoft Azure"
teaser: "In this lab, you will create a weather station that responds to records temperature and humidity."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [esp8266, lua, MQTT, iot, maker]
permalink: /workshop/dev-days/esp8266/weather/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab, you will create a simple _Thing_ using a ESP8266 and the Lua programming language. 

# Bill of Materials
In this lab series you will need the following:

1. [Microsoft Azure IoT Starter Kit w/ Adafruit Feather HUZZAH](https://www.adafruit.com/product/3032), including
    - DHT22
        
# Overview
In this lab, you will combine the LED you previously built with a DHT22 -- which measures temperature and humidity. You will collect the temperature and humidity and use it as a minimal weather station. This data will get sent to Azure so you can start exploring the data processing pipeline for IoT data. 

# Connecting the Sensors
Connect the DHT22 to the ESP8266 (Huzzah Feather) as shown in these images:

<img src="/images/esp8266-dht.png" alt="Connecting DHT to Rails"/>

* Connect the DHT22 Left Connection (when facing it) to +3V
* Connect the DHT22 Right Connection (when facing it) to Ground
* Connect the DHT22 Connection 2nd from Left when facing it, to Huzzah Feather Pin 12 (NodeMCU Pin 6)

# Create the Lua Program in ESPlorer 

As in the previous lab, you'll write lua code in ESplorer.

1. Launch ESPlorer.jar, select your serial port, and press the 'Open' button

<img src="/images/esplorer-connect.png" alt="Launch Esplorer, connect your device" style="width: 600px;"/>

2. Create a new script in Esplorer with these contents. Save it as Lab03.lua

{% highlight lua %}
-- LAB 03: Weather
-- This program collects temperature and humidity from the DT22.

-- Symbolic names for the pins
ONBOARD  = 3
REDLED   = 1
GREENLED = 2
BLUELED  = 5
DHTPIN   = 6

-- Set the GPIO pins controlling the LED to output mode.
-- There are three pins, one each for: Red, Green, and Blue.
gpio.mode(ONBOARD,  gpio.OUTPUT)
gpio.mode(REDLED,   gpio.OUTPUT)
gpio.mode(GREENLED, gpio.OUTPUT)
gpio.mode(BLUELED,  gpio.OUTPUT)

-- The do_work function blinks both the LEDs and samples the temprature and humidity sensor
function do_work()
    -- Turn the LEDs ON when we run this routine
    gpio.write(ONBOARD, gpio.LOW)
    gpio.write(REDLED, gpio.LOW)
    gpio.write(GREENLED, gpio.LOW)
    gpio.write(BLUELED, gpio.LOW)
    
    status, temperature, humidity, temperature_dec, humidity_dec = dht.read(DHTPIN)
    if status == dht.OK then
        print("Temperature: "..temperature.." ".." Humidity: "..humidity)
    elseif status == dht.ERROR_CHECKSUM then
        print( "DHT Checksum error." )
    elseif status == dht.ERROR_TIMEOUT then
        print( "DHT timed out." )
    end
    
    -- Turn the LEDs OFF when we exit this routine
    gpio.write(ONBOARD, gpio.HIGH)
    gpio.write(REDLED, gpio.HIGH)
    gpio.write(GREENLED, gpio.HIGH)
    gpio.write(BLUELED, gpio.HIGH)
end

-- Create a timer 
--   Timer id = 1
--   Duration = 500 microseconds (1/2 second)
--   Mode     = tmr.ALARM_AUTO (reregister to do it again forever)
--   Callback = do_work
tmr.alarm(1, 500, tmr.ALARM_AUTO, do_work)
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

1. Working with a sensor.
2. Maintaining state while constantly polling sensors for state changes.

In the [next lab][nextlab] you will set up an Azure IoT Hub to use with the ThingLabs Weather Station. 

<a class="radius button small" href="{{ site.url }}/workshop/dev-days/esp8266/setup-azure-iot-hub/">Setting Up Azure IoT</a>

[nextlab]: /workshop/dev-days/esp8266/setup-azure-iot-hub/