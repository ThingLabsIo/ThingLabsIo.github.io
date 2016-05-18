---
layout: "page-fullwidth"
title: "Sending Device-to-Cloud (D2C) Messages"
subheadline: "Building Connected Things with an ESP8266 and Microsoft Azure"
teaser: "In this lab, you will build send messages from the Thingy you have built to the cloud."
show_meta: true
comments: true
header: no
breadcrumb: true
categories: [esp8266, lua, MQTT, iot, maker, azure, azure-iot-hub]
permalink: /workshop/esp8266/sending-d2c-messages/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

# Bill of Materials
What you will need:

1. The ThingLabs Weather Station created in the ['ThingLabs Weather' lab](../weather/).

# Connecting your Weather Station to the Cloud

This lab will show you how to restructure your existing simple application into an application that uses the MQTT protocol to send telemetry data to Azure. On the Azure side we're using the MQTT gateway to accept messages instead of the default AMQP protocol IoTHub's support.

From [Wikipedia's MQTT Article](https://en.wikipedia.org/wiki/MQTT):
    
> MQTT[1] (formerly MQ Telemetry Transport) is an ISO standard (ISO/IEC PRF 20922)[2] publish-subscribe based "light weight" messaging protocol for use on top of the TCP/IP protocol. It is designed for connections with remote locations where a "small code footprint" is required or the network bandwidth is limited. The publish-subscribe messaging pattern requires a message broker. The broker is responsible for distributing messages to interested clients based on the topic of a message. Andy Stanford-Clark and Arlen Nipper of Cirrus Link Solutions authored the first version of the protocol in 1999.

The structure of the [NodeMCU MQTT client](http://nodemcu.readthedocs.io/en/dev/en/modules/mqtt/) is documented to provide both telemetry and command processing. For this lab we focus just on telemetry. An example is provided from the NodeMCU documents:

{% highlight lua %}
-- init mqtt client with keepalive timer 120sec
m = mqtt.Client("clientid", 120, "user", "password")

-- setup Last Will and Testament (optional)
-- Broker will publish a message with qos = 0, retain = 0, data = "offline" 
-- to topic "/lwt" if client don't send keepalive packet
m:lwt("/lwt", "offline", 0, 0)

m:on("connect", function(client) print ("connected") end)
m:on("offline", function(client) print ("offline") end)

-- on publish message receive event
m:on("message", function(client, topic, data) 
  print(topic .. ":" ) 
  if data ~= nil then
    print(data)
  end
end)

-- for TLS: m:connect("192.168.11.118", secure-port, 1)
m:connect("192.168.11.118", 1883, 0, function(client) print("connected") end, 
                                     function(client, reason) print("failed reason: "..reason) end)

-- Calling subscribe/publish only makes sense once the connection
-- was successfully established. In a real-world application you want
-- move those into the 'connect' callback or make otherwise sure the 
-- connection was established.

-- subscribe topic with qos = 0
m:subscribe("/topic",0, function(client) print("subscribe success") end)
-- publish a message with data = hello, QoS = 0, retain = 0
m:publish("/topic","hello",0,0, function(client) print("sent") end)

m:close();
-- you can call m:connect again
{% endhighlight %}

Adapting this example, we have the program below that connects to the MQTT IoTHub Gateway and sets a timer to send telemetry every 1 second.

# Create the Lua Program in ESPlorer 

As in the previous lab, you'll write lua code in ESplorer. This code (below), operates the photocell and LED as a nightlight.

1. Launch ESPlorer.jar, select your serial port, and press the 'Open' button

<img src="/images/esplorer-connect.png" alt="Launch Esplorer, connect your device" style="width: 400px;"/>

2. Cut and Paste or write this code into the left side of the ESPlorer

{% highlight lua %}
--
-- LAB 04: Sending Device-to-Cloud (D2C) Messages
--
-- This program sends the telemetry (light, temperature and humidity) and sends it to the cloud.
--

-- Configuration to connect to the MQTT broker.
DEVICE = "DEVICE ID" -- like "ThingLabs-esp8266"
IOTHUB = "IOTHUB DNS NAME" -- like "ThingLabsIoTHub.azure-devices.net"  
IOTHUB_DEVICE_CONNECTION_KEY = "Device Connect Key" -- like "5AohmbYte0vuQtkpUCxTRjcnQOe5bmU2zaotv9hUS/k="

-- Standard variables for connecting via MQTT to IoTHUB Do Not Change
PORT   = 8883
USER   = IOTHUB.."/"..DEVICE

-- Pin Assignment Variables from the previous Labs
ADCPIN = 0
LEDPIN = 3
DHTPIN = 6
MAXLIGHT = 1024

-- Generate a SAS Token for Azure IoTHub
PASSWD = generateSasToken(IOTHUB..'/devices/'..DEVICE, IOTHUB_DEVICE_CONNECTION_KEY, 'device', 7 * 24 * 60)

-- MQTT topics to subscribe
telemetry_topic="devices/"..DEVICE.."/messages/events/"
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

function setup()    
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

## Monitor the Messages Being Received by Azure IoT Hub
Using the Device Explorer utility for Windows you installed in the [previous lab](../setup-azure-iot-hub/), you can monitor the messages being received in Azure IoT Hub.

1. Open the __Data__ tab.
2. Select the device from the drop-down list.
3. Click __Monitor__ to begin monitoring messages as they come into your Azure IoT Hub.

# Conclusion &amp; Next Steps
Congratulations! In this lab, you updated the __Weather Station__ application to send messages to Azure IoT Hub. The core concepts you've learned are:

1. Using the Azure IoT MQTT Gateway to receive data sent using MQTT to send device-to-cloud (D2C) messages.
2. Sending messages as a continuous stream (e.g. once per second from a continuously measuring sensor).

At this point, nothing interesting is happening in the cloud with that data you are sending to Azure. It is simply being persisted for a default amount of time (1-day) and then being dropped. In the [next lab][nextlab], you will create a web application to visualize the data.

<a class="radius button small" href="{{ site.url }}/workshop/esp8266/storing-displaying-data/">Go to 'ThingLabs Storing &amp; Displaying Data' ›</a>

[nextlab]: /workshop/esp8266/storing-displaying-data/