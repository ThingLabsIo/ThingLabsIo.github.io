---
layout: "page-fullwidth"
title: "Lab 07: Sending Cloud-to-Device (C2D) Messages"
subheadline: "Building Connected Things with an ESP8266 and Microsoft Azure"
teaser: "In this lab, you will use the Azure Web App from Lab 06 to control the ThingLabs Weather Station remotely. The Web App will send messages to the Weather Station via Azure, which the Weather Station will receive, process, and act upon."
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


# Create the Lua Program in ESPlorer 

As in the previous lab, you'll write lua code in ESplorer. The code below, operates the photocell and LED as a nightlight.

1. Use an A to Micro B USB cable to connect the ESP2866 to your computer.
 
2. Launch ESPlorer.jar, select your serial port, and press the 'Open' button.

<img src="/images/esplorer-connect.png" alt="Launch Esplorer, connect your device" style="width: 400px;"/>

3. Create a new script in Esplorer with these contents. Save it as Lab07.lua

{% highlight lua %}
--
-- LAB 07: Receiving Cloud-to-Device (C2D) Messages
--
-- This program sends the telemetry (light, temperature and humidity) and sends it to the cloud.
-- It also receives commands from the cloud to turn the RGB LED on and off.
--

-- Configuration to connect to the MQTT broker.
DEVICE = "THingLabs-esp8266"
IOTHUB = "ThingLabsIoTHub.azure-devices.net"
IOTHUB_DEVICE_CONNECTION_KEY = "XBoriIl+BZTpz6Yk84SPtDxel+wINQy1izIOfEQ4E3w="

-- Standard variables for connecting via MQTT to IoTHUB Do Not Change
PORT   = 8883
USER   = IOTHUB.."/"..DEVICE

-- Pin Assignment Variables from the previous Labs
ADCPIN   = 0
REDLED   = 1
GREENLED = 2
LEDPIN   = 3
BLUELED  = 5
DHTPIN   = 6
MAXLIGHT = 1024
TELEMETRY_TIMER = 2

-- Generate a SAS Token for Azure IoTHub
PASSWD = generateSasToken(IOTHUB.."/devices/"..DEVICE, IOTHUB_DEVICE_CONNECTION_KEY, 7 * 24 * 60)

-- MQTT topics to subscribe
telemetry_topic="devices/"..DEVICE.."/messages/events/"
command_topic="devices/"..DEVICE.."/messages/devicebound/#"

-- Setting up the RGB LED
pwm.setup(REDLED,   500, 1023)
pwm.setup(GREENLED, 500, 1023)
pwm.setup(BLUELED,  500, 1023)
pwm.start(REDLED)
pwm.start(GREENLED)
pwm.start(BLUELED)

-- On Board LED
gpio.mode(LEDPIN, gpio.OUTPUT)

-- LED Helper function
function led(r, g, b)
    pwm.setduty(REDLED, r)
    pwm.setduty(GREENLED, g)
    pwm.setduty(BLUELED, b)
end

-- Sample publish functions:
function publish_data()
    -- Turn the LED on
    gpio.write(LEDPIN, gpio.LOW)
    light = MAXLIGHT - adc.read(ADCPIN)
    
    status, temperature, humidity, temperature_dec, humidity_dec = dht.read(DHTPIN)
    if status == dht.OK then
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

    -- Turn the LED off
    gpio.write(LEDPIN, gpio.HIGH)
end

-- Create an MQTT Client
esp8266 = mqtt.Client(DEVICE, 240, USER, PASSWD)

-- callback handler for incoming messages
esp8266:on("message", function(client, topic, message)
    print("MQTT Message.")
    if message ~= nil then
        print("Message: "..message)
    end

    jsondata = cjson.decode(message)
    for k,v in pairs(jsondata) do print(k,v) end
            
    -- jsondata.ledState = 0 | 1
    if jsondata.ledState == 1 then
        pwm.setduty(REDLED,   0)
        pwm.setduty(GREENLED, 0)
        pwm.setduty(BLUELED,  0)
    end
    if jsondata.ledState == 0 then
        pwm.setduty(REDLED,   1023)
        pwm.setduty(GREENLED, 1023)
        pwm.setduty(BLUELED,  1023)
    end
end)

-- Connect to IoTHub via MQTT
print "Connecting to MQTT broker. Please wait..."
esp8266:connect(IOTHUB, PORT, 1, 0, 
    -- Callback for a successful connection
    function(client) 
        print("Connected to MQTT: "..IOTHUB..":"..PORT.." as "..DEVICE) 
        -- Subscribe to command & control messages
        esp8266:subscribe(command_topic, 0, function(client) print("Subscribed to command and control topic.") end)
        -- Register Timer to send telemetry
        tmr.alarm(TELEMETRY_TIMER, 5000, tmr.ALARM_AUTO, publish_data)
    end,
    -- Error callback, if connection fails
    function(client, reason) 
        print("Error Connecting: "..reason) 
    end
)
{% endhighlight %}

# Run the Lua Program on the ESP8266
To run Lab07.lua on your Weather Station, do the following:

1. Press the button 'Save to ESP' on the lower left of the ESPlorer interface.
2. Push the reset button on the ES8266.
3. When it's done booting, click the 'Reload' button on the right side.
   You should see a list of files on the ESP8266
4. Double click the file on the right hand side that you saved.
   This should execute your code.

# Run the Lua Program Again Turning the LED On and Off
Now you can browse to the website you deployed in Lab 05 and use the buttons to turn the LED on and off.

1. Browse to your website and enter the name of your device as the __device id__.
2. Press the __LED ON__ button and see your blue led turn on.
3. Press the __LED OFF__ button and see your blue led turn off.

![Send LED ON and OFF messages](/images/workshops/thingy-4-windows/sending-c2d-messages.png) 

# Conclusion
Congratulations! In this lab, you updated the __Weather Station__ application to receive messages from a website through Azure IoT Hub. The core concepts you've learned are:

1. Using the NodeMCU, Lua and MQTT to handle cloud-to-device (C2D) messages.
