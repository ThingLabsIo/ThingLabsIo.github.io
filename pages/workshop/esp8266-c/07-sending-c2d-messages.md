---
layout: "page-fullwidth"
title: "Sending Cloud-to-Device (C2D) Messages"
subheadline: "Building Connected Things with an ESP8266 and Microsoft Azure"
teaser: "In this lab you will extend your website by adding the ability to control the ThingLabs Thingy&trade; remotely. The website will send messages to the Thingy via Azure."
show_meta: true
comments: true
header: no
breadcrumb: true
categories: [esp8266, lua, MQTT, iot, maker, azure, azure-iot-hub]
permalink: /workshop/esp8266/sending-c2d-messages/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will extend your website by adding the ability to control the ThingLabs Weather Station remotely. The website will send messages to the Thingy via Azure.

# Create the Lua Program in ESPlorer 

As in the previous lab, you'll write lua code in ESplorer. This code (below), operates the photocell and LED as a nightlight.

1. Launch ESPlorer.jar, select your serial port, and press the 'Open' button

<img src="/images/esplorer-connect.png" alt="Launch Esplorer, connect your device" style="width: 400px;"/>

2. Create a new script in Esplorer with these contents. Save it as Lab05.lua

{% highlight lua %}
--
-- LAB 05: Receiving Cloud-to-Device (C2D) Messages
--
-- This program receives commands from the cloud and executes them.
--

-- Configuration to connect to the MQTT broker.
DEVICE = "ThingLabs-esp8266"
IOTHUB = "ThingLabsIoTHub.azure-devices.net"  
IOTHUB_DEVICE_CONNECTION_KEY = "5AohmbYte0vuQtkpUCxTRjcnQOe5bmU2zaotv9hUS/k="

-- Standard variables for connecting via MQTT to IoTHUB Do Not Change
PORT   = 8883
USER   = IOTHUB.."/"..DEVICE

-- Pin Assignment Variables from the previous Labs
REDLED   = 1
GREENLED = 2
BLUELED  = 5
ADCPIN = 0
LEDPIN = 3
DHTPIN = 6
MAXLIGHT = 1024

-- Generate a SAS Token for Azure IoTHub
PASSWD = generateSasToken(IOTHUB..'/devices/'..DEVICE, IOTHUB_DEVICE_CONNECTION_KEY, 'device', 7 * 24 * 60)

-- MQTT topics to subscribe
telemetry_topic="devices/"..DEVICE.."/messages/events/"
command_topic="devices/"..DEVICE.."/messages/devicebound/#"
connected = false

-- Create an MQTT Client
esp8266 = mqtt.Client(DEVICE, 240, USER, PASSWD)

-- Connect to IoTHub via MQTT
print "Connecting to MQTT broker. Please wait..."
esp8266:connect(IOTHUB, PORT, 1, 0, 
    -- Callback for a successful connection
    function(client)
        print("Connected to MQTT: "..IOTHUB..":"..PORT.." as "..DEVICE)
        connected = true
        setup()        
    end,
    -- Error callback, if connection fails
    function(client, reason)
        print("Error Connecting: "..reason)
    end
)

function led(r, g, b)
    pwm.setduty(REDLED, r)
    pwm.setduty(GREENLED, g)
    pwm.setduty(BLUELED, b)
end

function setup()    
    esp8266:subscribe(command_topic, 0, function(client)
        connected = true
        print("Subscribed to command and control topic.")
    end)

    tmr.alarm(2, 1000, tmr.ALARM_AUTO, publish_data)

    -- If we get disconnected a callback informs us
    esp8266:on("offline", function(client)
        print("MQTT Disconnected.")
        connected = false
    end)
end

-- Sample publish functions:
function publish_data()
    if connected == true then
        -- Turn the LED on
        gpio.write(LEDPIN, gpio.LOW)
        light = MAXLIGHT - adc.read(ADCPIN)
        
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
            print("Light: "..light.." Temperature: "..temperature.." ".."Humidity: "..humidity)
        elseif status == dht.ERROR_CHECKSUM then
            print( "DHT Checksum error." )
        elseif status == dht.ERROR_TIMEOUT then
            print( "DHT timed out." )
        end
        
        -- Construct the payload of data
        payload =  
            "{ \"deviceId\" : \""..DEVICE.."\","..
            "\"location\" : \"Instructor ESP8266 Virtual Weather Station\""..","..
            "\"celsius\" :"..temperature..","..
            "\"relativeHumidity\" :"..humidity..","..
            "\"lightLevel\" :"..light.."}"
    
        -- Send it to the cloud via mqtt
        esp8266:publish(telemetry_topic, payload, 1, 0, function(client)
            print("Data published successfully.")
        end)

        -- Set the LED Brightness
        led(light, light, light)
    
        -- Turn the LED off
        gpio.write(LEDPIN, gpio.HIGH)
    end
end
{% endhighlight %}

# Run the App on the Device
To run the application you will save it to the ESP8266, reset the device, then invoke the code.

1. Press the button 'Save to ESP' on the lower left of the ESPlorer interface.
2. Push the reset button on the ES8266
3. When it's done booting, click the 'Reload' button on the right side.
   You should see a list of files on the ESP8266
4. Double click the file on the right hand side that you saved.
   This should execute your code.

# Run the Application Again Turning the LED On and Off
Now you can browse to the website you deployed in Lab 05 and use the buttons to turn the LED on and off.

1. Browse to your website and enter the name of your device as the __device id__.
2. Press the __LED ON__ button and see your blue led turn on.
3. Press the __LED OFF__ button and see your blue led turn off.

![Send LED ON and OFF messages](/images/workshops/thingy-4-windows/sending-c2d-messages.png) 

# Conclusion
Congratulations! In this lab, you updated the __Weather Station__ application to receive messages from a website through Azure IoT Hub. The core concepts you've learned are:

1. Using the NodeMCU, Lua and MQTT to handle cloud-to-device (C2D) messages.
