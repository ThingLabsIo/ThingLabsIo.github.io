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

# Startup Scripts for your ESP8266 to use the Cloud

Before you can run Cloud code on your device you need a couple of important pieces:

1. You need to connect to a wifi network
2. You need to set the clock (The ESP8266 has a real-time clock that's reliable once it's set)
3. You need some extra code to generate security tokens to access Azure.

The following two scripts work together to initialize your ESP8266 to have these pieces. NodeMCU will look for an __init.lua__ when it boots and execute it, so you have to be sure it's correct.

The __init.lua__ provided below invokes the __startup.lua__ to set the clock (which requires wifi), so you need to save the startup.lua first, before you save the init.lua or things will break.

In Esplorer create a new script and paste the following code into it. Then save the file as your __startup.lua__ and save to the ESP8266.

{% highlight lua %}
function char_to_pchar(c)
    return string.format("%%%02X", c:byte(1,1))
end
function encodeURI(str)
    return (str:gsub("[^%;%,%/%?%:%@%&%=%+%$%w%-%_%.%!%~%*%'%(%)%#]", char_to_pchar))
end
function encodeURIComponent(str)
    return (str:gsub("[^%w%-_%.%!%~%*%'%(%)]", char_to_pchar))
end

function _(hex) return string.char(tonumber(hex, 16)) end

function decodeURI(str)
    str = string.gsub(str, '%%(%x%x)', _)
    return str
end

function generateSasToken(resourceUri, signingKey, expiresInMinutes)
    resourceUri = string.lower(encodeURIComponent(string.lower(resourceUri)));
    -- Set expiration
    sec, usec = rtctime.get()
    expires = math.ceil(sec + expiresInMinutes * 60)
    toSign = resourceUri.."\n"..expires
    -- add the password
    decodedPassword = encoder.fromBase64(signingKey)
    base64signature = crypto.toBase64(crypto.hmac("SHA256", toSign, decodedPassword))
    base64UriEncoded = encodeURIComponent(base64signature)
    -- construct the authorization string
    token = "SharedAccessSignature sr="..resourceUri.."&sig="..base64UriEncoded.."&se="..expires;
    token = token.."&skn="
    return token    
end

sntp_connect_status_codes = {
    [1] = "DNS lookup failed",
    [2] = "Memory allocation failure",
    [3] = "UDP send failed",
    [4] = "Timeout, no NTP response received"
}

-- sync the clock via NTP
sntp.sync('pool.ntp.org',
  function(sec, usec, server)
    print("Clock Synced: "..sec..", "..usec..", "..server)
    end,
  function(error_code)
    print("Clock Sync Failed: "..sntp_connect_status_codes[error_code])
  end
)
{% endhighlight %}

In Esplorer create a new script and paste the following code into it. Then save the file as your __init.lua__ and save to the ESP8266.

{% highlight lua %}
-- Diagnostic Information
print('init.lua ver 1.2') 
wifi.setmode(wifi.STATION)
print('set mode=STATION (mode='..wifi.getmode()..')')
print('MAC: '..wifi.sta.getmac())
print('chip: '..node.chipid())
print('heap: '..node.heap())

-- Connect to Wifi
wifi.sta.config("SSID", "PASSWORD")

wifi_status_codes = {
    [0] = "Idle",
    [1] = "Connecting",
    [2] = "Wrong Password",
    [3] = "No AP Found",
    [4] = "Connection Failed",
    [5] = "Got IP"
}

-- Wait for wifi to get connected, fail if it doesn't
tmr.alarm(1,1000, 1, function() 
    if wifi.sta.getip()==nil then 
        print("Waiting for IP address! (Status: "..wifi_status_codes[wifi.sta.status()]..")") 
    else 
        print("New IP address is "..wifi.sta.getip()) 
        tmr.stop(1) 
    end 
  end
)

-- Launch Next Startup File
tmr.alarm(0, 5000, 0, function() -- Zero as third parameter. Call once the file.
   dofile('startup.lua')
end)
{% endhighlight %}


# Create the Lua Program in ESPlorer 

As in the previous lab, you'll write lua code in ESplorer. This code (below), operates the photocell and LED as a nightlight.

1. Launch ESPlorer.jar, select your serial port, and press the 'Open' button

<img src="/images/esplorer-connect.png" alt="Launch Esplorer, connect your device" style="width: 400px;"/>

2. Create a new script in Esplorer with these contents. Save it as Lab04.lua

If you recall, you should have your connection from before and it should look something like this:

HostName=ThingLabsIoTHub.azure-devices.net;DeviceId=ThingLabs-esp8266;SharedAccessKey=XBoriIl+BZTpz6Yk84SPtDxel+wINQy1izIOfEQ4E3w=

From this, you insert the DeviceId(ThingLabs-esp8266) as your DEVICE, the HostName (ThingLabsIoTHub.azure-devices.net) as your IOTHUB, and the SharedAccesskey (XBoriIl+BZTpz6Yk84SPtDxel+wINQy1izIOfEQ4E3w=) as your IOTHUB_DEVICE_CONNECTION_KEY. It should look like this:

DEVICE = "THingLabs-esp8266"
IOTHUB = "ThingLabsIoTHub.azure-devices.net"
IOTHUB_DEVICE_CONNECTION_KEY = "XBoriIl+BZTpz6Yk84SPtDxel+wINQy1izIOfEQ4E3w="

{% highlight lua %}
--
-- LAB 04: Sending Device-to-Cloud (D2C) Messages
--
-- This program sends the telemetry (light, temperature and humidity) and sends it to the cloud.
--

-- Configuration to connect to the MQTT broker.
DEVICE = "DEVICE ID" 
IOTHUB = "IOTHUB DNS NAME"   
IOTHUB_DEVICE_CONNECTION_KEY = "DEVICE CONNECTION KEY"

-- Standard variables for connecting via MQTT to IoTHUB Do Not Change
PORT   = 8883
USER   = IOTHUB.."/"..DEVICE

-- Pin Assignment Variables from the previous Labs
ADCPIN = 0
LEDPIN = 3
DHTPIN = 6
MAXLIGHT = 1024

-- Generate a SAS Token for Azure IoTHub
PASSWD = generateSasToken(IOTHUB..'/devices/'..DEVICE, IOTHUB_DEVICE_CONNECTION_KEY, 7 * 24 * 60)

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