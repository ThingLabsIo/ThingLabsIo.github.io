---
layout: page-fullwidth
title: "Sending Telemetry to the Cloud"
subheadline: "Building Connected Things with Node.js"
teaser: "In this lab you will gather telemetry and send it to the cloud."
show_meta: true
comments: true
header: no
breadcrumb: true
categories: [azure, azure-iot-hub, javascript, node.js, johnny-five, arduino, photon]
author: doug_seven
permalink: /tr22/js/sending-telemetry/
---
# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will write a Node.js application that runs on a hub (your development machine) and collects data from a development board and sends it up to your Azure IoT Hub.

# Setup the App Dependencies
Since you will be using Node.js for this lab you can take advantage of the dependency management capabilities that Node.js and NPM provide. You need to let your application know that it has a dependency on the following NPM packages - Azure IoT Device, Johnny-Five and Particle-IO. In Node.js this is done with a _package.json_ file. This file provides some basic meta-data about the application, including any dependencies on packages that can be retrieved using NPM (according to [npmjs.com](https://www.npmjs.com) today, NPM stands for Narrating Prophetic Monks...not Node Package Manager like you may have thought).

Using your favorite/preferred text/code editor, create a file in your development directory named __package.json__ and add the following:

{% highlight json %}
{
  "name": "IoT-Labs",
  "version": "0.1.2",
  "private":true,
  "description": "Sample app that connects a device to Azure using Node.js",
  "main": "weather.js",
    "author": "YOUR NAME HERE",
  "license": "MIT",
  "dependencies": {
    "azure-iot-device": "1.0.0",
    "azure-iot-device-amqp": "1.0.0",
    "azure-iot-device-amqp-ws": "1.0.0",
    "azure-iot-device-http": "1.0.0",
    "azure-iot-device-mqtt": "1.0.0",
    "johnny-five": "0.9.19",
    "j5-sparkfun-weather-shield": "0.2.0",
    "particle-io": "0.12.0"
  }
}
{% endhighlight %}

With the _package.json_ file created you can use NPM to pull down the necessary Node modules. Open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following commands (replace _C:\Development\IoTLabs_ with the path that leads to your development directory):

On Windows, open the Node.js command prompt and type the following:

<pre>
  cd C:\Development\IoTLabs
  npm install
</pre>

On Mac OS X open Terminal and type the following:

<pre>
  cd ~/Development/IoTLabs
  npm install
</pre>

Next you will create the application code to gather temperature, humidity, and barometer data and send it to the cloud.

<blockquote>
    In order to complete this step you will need the device ID and access token from Particle Cloud (the cloud service that the Photon is coupled to). For the TechReady 22 labs you are sharing an existing Particle Cloud account and your Photon has already been configured. 

    <ul>
        <li>The device ID is <b>PhotonThingXX</b> where XX is the number on your Photon</li>
    </ul>
</blockquote>

## Write the Weather App
The Weather app will run on your gateway (your development machine for now) and communicate between your _Thing_ and your Azure IoT Hub. Create another file in the same directory named __weather.js__.

The first thing you need to do is define the objects you will be working with in the application. The four things that matter are the Azure IoT _device_ object, a Johnny-Five framework object, the Weather Shield plugin object, and an object to represent the physical board you are working with. 

{% highlight javascript %}
// https://github.com/ThingLabsIo/IoTLabs/tree/master/Photon/Weather
'use strict';
// Define the objects you will be working with
var five = require ("johnny-five");
var Shield = require("j5-sparkfun-weather-shield")(five);
var device = require('azure-iot-device');

// Add the following definition for the Particle plugin for Johnny-Five
var Particle = require("particle-io");

// Specify the Particle Cloud access token
var token = '65dbc7982187b1c1b1d34ed7ebad8f870c3b7e92';
// location is simply a string that you can filter on later
var location = 'TechReady22';

var connectionString = 'YOUR IOT HUB DEVICE-SPECIFIC CONNECTION STRING FROM THE DEVICE EXPLORER';

// Define the protocol that will be used to send messages to Azure IoT Hub
// For this lab we will use AMQP over Web Sockets.
// If you want to use a different protocol, comment out the protocol you want to replace, 
// and uncomment one of the other transports.
var Protocol = require('azure-iot-device-amqp-ws').AmqpWs;
// var Protocol = require('azure-iot-device-amqp').Amqp;
// var Protocol = require('azure-iot-device-http').Http;
// var Protocol = require('azure-iot-device-mqtt').Mqtt;

// Define the client object that communicates with Azure IoT Hubs
var Client = require('azure-iot-device').Client;
// Define the message object that will define the message format going into Azure IoT Hubs
var Message = require('azure-iot-device').Message;

// Create the client instanxe that will manage the connection to your IoT Hub
// The client is created in the context of an Azure IoT device.
var client = Client.fromConnectionString(connectionString, Protocol);
// Extract the Azure IoT Hub device ID from the connection string 
// (this may not be the same as the Photon device ID)
var deviceId = device.ConnectionString.parse(connectionString).DeviceId;
console.log("Device ID: " + deviceId);

// Create a Johnny-Five board instance to represent your Particle Photon
// Board is simply an abstraction of the physical hardware, whether is is a 
// Photon, Arduino, Raspberry Pi or other boards. 
// When creating a Board instance for the Photon you must specify the token and device ID
// for your Photon using the Particle-IO Plugin for Johnny-five.
// Replace the Board instantiation with the following:
var board = new five.Board({
  io: new Particle({
    token: token,
    deviceId: 'PhotonThingXX'
  })
});
{% endhighlight %}

In this code you define four variables that you will be working with:

1. <code>five</code> - represents the Johnny-Five framework capabilities, which provide a type of object model for working with boards like Arduino and Particle.
2. <code>Shield</code> - represents the Weather Shield plugin for Johnny-Five, which is compatible with both the Arduino and Photon variants. 
4. <code>board</code> - represents the physical board you are using. Particle Photon users must pass an instance of the Particle-IO plugin object to the Board constructor, specifying the Particle access token and the device ID or alias.
5. <code>client</code> - the agent class that facilitates the communication between your device and the Azure IoT Hub. It takes the device-specific connection string as an argument and establishes a connection with the IoT Hub.

Now that the objects are created, you can get to the meat of the application. Johnny-Five provides a board 'ready' event that makes a callback when the board is on, initialized and ready for action. Inside the anonymous callback function is where your application code executes (this function is invoked when the board is ready for use).

Johnny-Five provides a collection of objects that represent the board, the pins on the board, and various types of sensors and devices that could be connected to the board. The <code>Shield</code> plug-in that you specified earlier is a software representation of the physical SparkFun Weather Shield that abstracts the Johnny-Five <code>Temperature</code> and <code>Barometer</code> classes that represent the HTU21D humidity sensor and the MPL3115A2 barometric pressure sensor respectively. When you create an instance of the <code>Shield</code> class you will specify the variant of the <code>Shield</code> class -- either <code>PHOTON</code> or <code>ARDUINO</code>.

In the following code you will invoke the <code>board.on()</code> function which establishes a callback function that is invoked when the board is on, initialized and ready. All of the operational code for the board will be in the <code>board.on()</code> function (helper functions may exist outside the scope on the <code>board.on()</code> function). Within the <code>board.on()</code> function you will create an object reference to the weather shield. Similar to the <code>board</code> object, the object you create to reference the shield will have an <code>on()</code> function that establishes a callback that exposes the data read from the sensors on the shield.

{% highlight javascript %}
// The board.on() executes the anonymous function when the 
// board reports back that it is initialized and ready.
board.on("ready", function() {
    console.log("Board connected...");
    
    // Open the connection to Azure IoT Hub
    // When the connection respondes (either open or error)
    // the anonymous function is executed
    client.open(function(err) {
        console.log("Azure IoT connection open...");
        
        if(err) {
            // If there is a connection error, show it
            console.err('Could not connect: ' + err.message);
        } else {
            // If the client gets an error, handle it
            client.on('error', function (err) {
                console.error(err.message);
            });
            
            // If the connection opens, set up the weather shield object
            
            // The SparkFun Weather Shield has two sensors on the I2C bus - 
            // a humidity sensor (HTU21D) which can provide both humidity and temperature, and a 
            // barometer (MPL3115A2) which can provide both barometric pressure and humidity.
            // Controllers for these are wrapped in a convenient plugin class:
            var weather = new Shield({
                variant: "PHOTON",  // or ARDUINO
                freq: 1000,         // Set the callback frequency to 1-second
                elevation: 100      // Go to http://www.WhatIsMyElevation.com to get your current elevation
            });
            
            // The weather.on("data", callback) function invokes the anonymous callback function 
            // whenever the data from the sensor changes (no faster than every 25ms). The anonymous 
            // function is scoped to the object (e.g. this == the instance of Weather class object). 
            weather.on("data", function () {
                console.log("weather data event fired...");
                var payload = JSON.stringify({
                    deviceId: deviceId,
                    location: location,
                    // celsius & fahrenheit are averages taken from both sensors on the shield
                    celsius: this.celsius,
                    fahrenheit: this.fahrenheit,
                    relativeHumidity: this.relativeHumidity,
                    pressure: this.pressure,
                    feet: this.feet,
                    meters: this.meters
                });
                
                // Create the message based on the payload JSON
                var message = new Message(payload);
                // For debugging purposes, write out the message payload to the console
                console.log("Sending message: " + message.getData());
                // Send the message to Azure IoT Hub
                client.sendEvent(message, printResultFor('send'));
            });
        }
    });
});
    
// Helper function to print results in the console
function printResultFor(op) {
  return function printResult(err, res) {
    if (err) console.log(op + ' error: ' + err.toString());
  };
}
{% endhighlight %}

In this code you do a number of things:

1. <code>board.on()</code> - This function triggers the board to invoke the anonymous callback function as soon as the board is on and ready. All of the application code for the device is written inside this callback function.
2. <code>client.open(callback)</code> - This function opens the connection to Azure IoT Hub and invokes the callback when it gets a response. In this case, the callback is an anonymous function.
3. Define the <code>weather</code> object. This is a instance of the wrapper around the temperature and barometer on the weather shield (represented by the Shield object) connected to the board. When you instantiate the object, you can specify a frequency to collect the data from the sensors. Many sensors are capable of collecting data in fraction of a second intervals. You may not want to collect data and send it to your Azure IoT Hub that frequently. The <code>freq</code> property defines (in milliseconds) how often to raise an event to report the data from the sensor. In this example you are establishing the callback at a frequency of once per second for the <code>weather</code> object.
4. <code>message</code> is the object that represents the data you are sending to Azure IoT Hub. This is a JSON formatted message.

When <code>client.sendEvent()</code> is invoked, the JSON message is sent to Azure IoT Hub. For now, nothing happens with the message once it is received in your IoT Hub because you haven't set up anything that will capture the message and do something with it (we will get to that soon). By default the messages have a one-day retention time.

# Run the App
When you run the application it will execute on your computer, and thanks to Johnny-Five, it will connect with your board and work directly with it. Basically, your computer is acting as a gateway - or hub - and communicating with the board as one of potentially many devices (or spokes). If you continue on past the intro labs, in a future lab you will deploy the Node.js application to another device (like a Raspberry Pi) which will act as the gateway and connect to multiple spoke devices.

Open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following commands (replace c:\Development\IoTLabs with the path that leads to your labs folder):

<pre>
  cd C:\Development\IoTLabs
  node weather.js
</pre>

After the board initializes you will see messages printing out once per second. This is the message payload that is being sent to Azure IoT Hub.

<blockquote>
  If you downloaded the Device Explorer utility for Windows you can open the Data tab, select a device, and click Monitor to begin monitoring messages as they come into your Azure IoT Hub.
</blockquote>

When you want to quite the application, press <kbd>CTRL</kbd> + <kbd>C</kbd> twice to exit the program without closing the window (you may also have to press <kbd>Enter</kbd>). 

<blockquote>
  PARTICLE PHOTON USERS: After stopping the application press the Reset button on the Photon to prepare it for the next run.
</blockquote> 

## Conclusion &amp; Next Steps

In this lab you learned how to write a Node.js + Johnny-Five application that collects environment telemetry and sends it to Azure IoT Hub. In the next lab you will setup some Azure services to store and visualize the data.

<a class="radius button small" href="{{ site.url }}/tr22/js/visualize-iot-with-powerbi/">Go to 'Visualizing IoT Data with Power BI' ›</a>

[deviceexplorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md
