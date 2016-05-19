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
    - RGB LED
    - 3 - 560 Ohm Resistors

If you haven't already done so, follow the setup instructions at ['Getting Started']({{ site.url }}/workshop/esp8266/getting-started/).

# Assemble the Parts
Insert the Huzzah Feather carefully into the prototyping board

<img src="/images/huzzah-feather-board.png" alt="Assembled Parts" style="width: 400px;"/>

NOTE: We _do not_ have a battery in our kit!

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

**3V** - this is the output from the 3.3V regulator, it can supply 500mA peak (try to keep your current draw under 250mA so you have plenty for the ESP8266's power) 

**RST** - this is the reset pin for the ESP8266, pulled high by default. When pulled down to ground momentarily it will reset the ESP8266 system. This pin is 3.3V logic only

**EN (CH_PD)** - This is the enable pin for the ESP8266, pulled high by default. When pulled down to ground momentarily it will reset the ESP8266 system. This pin is 3.3V logic only

<img src="/images/huzzah-layout.png" alt="Huzzah Layout" style="width: 400px;"/>

**I2C SDA** = GPIO #4 (default) 

**I2C SCL** = GPIO #5 (default)

**SPI SCK** = GPIO #14 (default) 

**SPI MOSI** = GPIO #13 (default) 

**SPI MISO** = GPIO #12 (default)

**ADC** - Analog to Digital Converter Pin, 1.0V max.

Serial Communication Occurs over TX/RX:

**TX** - output from the module and is 3.3V logic.

**RX** - input into the module and is 5V compliant (there is a level shifter on this pin)

# Create the Lua Program in ESPlorer 
The Lua programming language is a simple language designed to avoid the clutter of many languages. It's powerful _enough_ and simple _enough_ to make it an effective language for working on devices. It's also much more user friendly than other languages typically used in small devices, like _forth_, and more flexible than _C_.

To write your first lua program for the ESP8266 you'll only have to write a few lines of code.

1. Launch ESPlorer.jar, select your serial port, and press the 'Open' button

<img src="/images/esplorer-connect.png" alt="Launch Esplorer, connect your device" style="width: 400px;"/>

2. Cut and Paste or write this code into the left side of the ESPlorer

{% highlight lua %}
--
-- LAB 01: Hello, Lua IoT!
--
-- This program blinks the on board LED, it is designed for the Huzzah Feather ESP8266 module.
--

-- Symbolic names for the pins
ONBOARD  = 3

-- Number of microseconds to leave the LED on
STAYLIT  = 250000 -- 1/4 second

-- Set the GPIO pin controlling the LED to output mode.
gpio.mode(ONBOARD, gpio.OUTPUT)

-- The flash_led function blinks the Green LED
function flash_led()
    -- LOW turns the LED on
    gpio.write(ONBOARD, gpio.LOW)
    -- We leave the LED on for 1/4 second
    tmr.delay(STAYLIT)       
    -- HIGH turns the LED off
    gpio.write(ONBOARD, gpio.HIGH)
end

-- Creates a timer 
--   Timer id = 1
--   Duration = 1000 milliseconds (1 second)
--   Mode     = tmr.ALARM_AUTO (reregister to do it again forever)
--   Callback = flash_led
tmr.alarm(1, 1000, tmr.ALARM_AUTO, flash_led)
{% endhighlight %}

That's all there is to it. Now you are ready to run the application. 

# Run the App on the Device
To run the application you will save it to the ESP8266, reset the device, then invoke the code.

1. Save the file, giving it a name: Lab01.lua
2. Press the button 'Save to ESP' on the lower left of the ESPlorer interface.
3. Push the reset button on the ES8266
4. When it's done booting, click the 'Reload' button on the right side.
   You should see a list of files on the ESP8266
5. Double click the file on the right hand side that you saved.
   This should execute your code.

# Wire the Huzzah Feather to the Power and Ground Rails

Connect the 3V pin to the Red column of holes marked with a '+'.
Connect the GND pin to the Blue column of holes marked with a '-'.

This image shows what it should look like:

<img src="/images/ESP8266-Rail-Wiring.jpeg" alt="Wire 3V and GND" style="width: 400px;"/>

# Add a Three Color LED from the kit

Find the three color LED and the 560 Ohm resistors, then wire it up to pins 1, 2, 5 (aka 4, 5, 14 on the Huzzah) as the following images illustrate:

<img src="/images/ESP8266-LED-Resistors.jpeg" alt="Connecting Resistors to LED" style="width: 400px;"/>

Then wire the LED to the power Rail, as shown here:

<img src="/images/ESP8266-LED-Power.jpeg" alt="Connecting Power Rail to LED" style="width: 400px;"/>

# Startup Scripts for your ESP8266

Before you can run Cloud code on your device you need a couple of important pieces:

1. You need to connect to a wifi network
2. You need to set the clock (The ESP8266 has a real-time clock that's reliable once it's set)
3. You need some extra code to generate security tokens to access Azure.

The following two scripts work together to initialize your ESP8266 to have these pieces. NodeMCU will look for an init.lua when it boots and execute it, so you have to be sure it's correct.

The init.lua provided below invokes the startup.lua to set the clock (which requires wifi), so you need to save the startup.lua first, before you save the init.lua or things will break.

In Esplorer create a new script and paste the following code into it. Then save the file as your startup.lua and save to the ESP8266.

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

function generateSasToken(resourceUri, signingKey, policyName, expiresInMinutes)
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
    if (policyName) then token = token.."&skn="..policyName end
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

In Esplorer create a new script and paste the following code into it. Then save the file as your init.lua and save to the ESP8266.

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

# Modify the code

Now that you have a three color LED wired to your ESP8266, you need to modify your Lua code to account for all of them. This code is modified to support the onboard LED and the new three colored LED you wired. Modify your code to match.

{% highlight lua %}
--
-- LAB 01: Hello, Lua IoT!
--
-- This program blinks an LED, it is designed for the Huzzah Feather
-- ESP8266 module.
--

-- Symbolic names for the pins
REDLED   = 1
GREENLED = 2
BLUELED  = 5
ONBOARD  = 3

-- Number of milliseconds to leave the LED on
STAYLIT  = 250

-- Set the GPIO pins controlling the LED to output mode.
-- There are three pins, one each for: Red, Green, and Blue.
gpio.mode(REDLED,   gpio.OUTPUT)
gpio.mode(GREENLED, gpio.OUTPUT)
gpio.mode(BLUELED,  gpio.OUTPUT)
gpio.mode(ONBOARD,  gpio.OUTPUT)

-- The flash_led function blinks the Green LED
function flash_led()
    -- LOW turns the LED on
    gpio.write(REDLED, gpio.LOW)
    gpio.write(GREENLED, gpio.LOW)
    gpio.write(BLUELED, gpio.LOW)
    gpio.write(ONBOARD, gpio.LOW)
    -- We leave the LED on for 1/4 second
    tmr.delay(STAYLIT)       
    -- HIGH turns the LED off
    gpio.write(REDLED, gpio.HIGH)
    gpio.write(GREENLED, gpio.HIGH)
    gpio.write(BLUELED, gpio.HIGH)
    gpio.write(ONBOARD, gpio.HIGH)
end

-- Creates a timer 
--   Timer id = 1
--   Duration = 1000 microseconds (1 second)
--   Mode     = tmr.ALARM_AUTO (reregister to do it again forever)
--   Callback = flash_led
tmr.alarm(1, 1000, tmr.ALARM_AUTO, flash_led)
{% endhighlight %}


# Run the Modified App on the Device

To run the application you will save it to the ESP8266, reset the device, then invoke the code.

1. Press the button 'Save to ESP' on the lower left of the ESPlorer interface.
2. Push the reset button on the ES8266
3. When it's done booting, click the 'Reload' button on the right side.
   You should see a list of files on the ESP8266
4. Double click the file on the right hand side that you saved.
   This should execute your code.

# Conclusion &amp; Next Steps

Congratulations! You have built a Lua application that controlled a device connected to an ESP8266. The core concepts you've learned are:

1. Building a Lua application that can run on an ESP8266. 

In the [next lab][nextlab], you will build a more complicated _Thing_ that uses both input sensors and output devices. 

<a class="radius button small" href="{{ site.url }}/workshop/esp8266/nightlight/">Go to 'Nightlight' ›</a>

[nextlab]: /workshop/esp8266/nightlight/