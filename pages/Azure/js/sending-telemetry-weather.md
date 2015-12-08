---
layout: page-fullwidth
title: "Sending Telemetry to the Cloud"
subheadline: "Connected Thing Labs using Azure and JavaScript"
teaser: "In this lab you will gather telemetry and send it to the cloud."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [azure, azure-iot-hub, javascript, node.js, johnny-five, arduino, photon]
author: doug_seven
permalink: /lang/js/weather/sending-telemetry/
---
### Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will write a Node.js application that runs on a hub (your development machine) and collects data from a development board and sends it up to your Azure IoT Hub.

# Bill of Materials
What you will need:

1. [SparkFun RedBoard Programmed with Arduino - $19.95](https://www.sparkfun.com/products/12757) or [Arduino Uno R3 - $24.95](https://www.sparkfun.com/products/11021) - __NOTE:__ The SparkFun Weather Shield will not work with the Arduino Y&uacute;n without modification.
2. [SparkFun Weather Shield for Arduino - $39.95](https://www.sparkfun.com/products/12081)
3. USB cable to connect the Arduino to your computer (the type will vary depending on the Arduino board you are using)

You must upload the Standard Firmatta to the board. See [Setting Up Your Arduino Firmware](/device/arduino/setup-arduino) for details.

__OR__

1. [Particle Photon Development Kit - $29.00][1]
2. [SparkFun Weather Shield for Particle Photon - $32.95](https://www.sparkfun.com/products/13630)
3. USB cable to connect the Photon to your computer

You must upload the VoodooSpark firmware to the Photon. See [Setting Up Your Particle Photon Firmware](/device/photon/setup-photon) for details.

# Setup the App Dependencies
Since you will be using Node.js for this lab you can take advantage of the dependency management capabilities that Node.js and NPM provide. You need to let your application know that it has a dependency on the following NPM packages - Azure IoT Device, Johnny-Five and Particle-IO. In Node.js this is done with a _package.json_ file. This file provides some basic meta-data about the application, including any dependencies on packages that can be retrieved using NPM (according to [npmjs.com](https://www.npmjs.com) today, NPM stands for Narrating Prophetic Monks...not Node Package Manager like you may have thought).

Using your favorite/preferred text/code editor, create a file in your development directory named __package.json__ and add the following:

{% highlight json %}
{
  "name": "IoT-Labs",
  "version": "0.1.0",
  "private":true,
  "description": "Sample app that connects a device to Azure using Node.js",
  "main": "weather.js",
    "author": "YOUR NAME HERE",
  "license": "MIT",
  "dependencies": {
    "azure-iot-device": "latest",
    "johnny-five": "latest",
    "j5-sparkfun-weather-shield": "latest"
  }
}
{% endhighlight %}

If you are using the Particle Photon, add the following line to the <code>dependencies</code> list:

{% highlight json %}
"particle-io": "latest"
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

You can verify the version of your Node.s install (remember, you want v0.12.x) using the <code>-v</code> flag.

<pre>
  node -v
  v0.12.7
</pre>

Next you will create the application code to gather temperature, humidity, and barometer data and send it to the cloud.

<blockquote>
  PARTICLE PHOTON USERS: In order to complete the next step, you will need the device ID you copied earlier when you were claiming the Photon (or the name/alias you gave the Photon when you updated the firmware to VoodooSpark) and your Particle Cloud access token. To get the access token, open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following command (you may be prompted to login or provide your Particle Cloud password again): 

  <pre>
    particle token list
  </pre>
  
  Find the token for _user_ (make sure if you see more than one that you choose the one that is not expired).
  
  Optionally you can use a browser to navigate to [Particle Build](https://build.particle.io/) and find your Access Token on the setting page (click on the gear icon in the lower-left part of the screen).
</blockquote>

## Write the Weather App
The Weather app will run on your gateway (your development machine for now) and communicate between your _Thing_ and your Azure IoT Hub. Create another file in the same directory named __weather.js__.

The first thing you need to do is define the objects you will be working with in the application. The four things that matter are the Azure IoT _device_ object, a Johnny-Five framework object, the Weather Shield plugin object, and an object to represent the physical board you are working with. 

{% highlight javascript %}
'use strict';
// Define the objects you will be working with
var five = require ("johnny-five");
var Shield = require("j5-sparkfun-weather-shield")(five);
var device = require('azure-iot-device');

/* 
// PARTICLE PHOTON USERS
// Add the following definition for the Particle plugin for Johnny-Five
var Particle = require("particle-io");

var token = 'YOUR PARTICLE ACCESS TOKEN HERE';
*/

var location = process.env.DEVICE_LOCATION || 'GIVE A NAME TO THE LOCATION OF THE THING';
var connectionString = process.env.IOTHUB_CONN || 'YOUR IOT HUB DEVICE-SPECIFIC CONNECTION STRING HERE';

// Create an Azure IoT client that will manage the connection to your IoT Hub
// The client is created in the context of an Azure IoT device, which is why
// you use a device-specific connection string.
var client = device.Client.fromConnectionString(connectionString);
var deviceId = device.ConnectionString.parse(connectionString).DeviceId;

// Create a Johnny-Five board instance to represent your Particle Photon
// Board is simply an abstraction of the physical hardware, whether is is a 
// Photon, Arduino, Raspberry Pi or other boards.
var board = new five.Board();
/* 
// PARTICLE PHOTON USERS
// When creating a Board instance for the Photon you must specify the token and device ID
// for your Photon using the Particle-IO Plugin for Johnny-five.
// Replace the Board instantiation with the following:
var board = new five.Board({
  io: new Particle({
    token: token,
    deviceId: 'YOUR PARTICLE PHOTON DEVICE IS OR ALIAS'
  })
});
*/
{% endhighlight %}

In this code you define four variables that you will be working with:

1. <code>five</code> - represents the Johnny-Five framework capabilities, which provide a type of object model for working with boards like Arduino and Particle.
2. <code>Shield</code> - represents the Weather Shield plugin for Johnny-Five, which is compatible with both the Arduino and Photon variants. 
4. <code>board</code> - represents the physical board you are using. Particle Photon users must pass an instance of the Particle-IO plugin object to the Board constructor, specifying the Particle access token and the device ID or alias.
5. <code>client</code> - the agent class that facilitates the communication between your device and the Azure IoT Hub. It takes the device-specific connection string as an argument and establishes a connection with the IoT Hub.

Now that the objects are created, you can get to the meat of the application. Johnny-Five provides a board 'ready' event that makes a callback when the board is on, initialized and ready for action. Inside the anonymous callback function is where your application code executes (this function is invoked when the board is ready for use).

Johnny-Five provides a collection of objects that represent the board, the pins on the board, and various types of sensors and devices that could be connected to the board. The <code>Shield</code> plug-in that you specified earlier is a software representation of the physical SparkFun Weather Shield that abstracts the Johnny-Five <code>Temperature</code> and <code>Barometer</code> classes that represent the HTU21D humidity sensor and the MPL3115A2 barometric pressure sensor respectively. When you create an instance of the <code>Shield</code> class you will specify the varient of the <code>Shield</code> class -- either <code>ARDUINO</code> or <code>PHOTON</code>.

In the following code you will invoke the <code>board.on()</code> function which establishes a callback function that is invoked when the board is on, initialized and ready. All of the operational code for the board will be in the <code>board.on()</code> function (helper functions may exist outside the scope on the <code>board.on()</code> function). Within the <code>board.on()</code> function you will create an object reference to the weather shield. Similar to the <code>board</code> object, the object you create to reference the shield will have an <code>on()</code> function that establishes a callback that exposes the data read from the sensors on the shield.

{% highlight javascript %}
// The board.on() executes the anonymous function when the 
// board reports back that it is initialized and ready.
board.on("ready", function() {
    console.log("Board connected...");
    
    // The SparkFun Weather Shield has two sensors on the I2C bus - 
    // a humidity sensor (HTU21D) which can provide both humidity and temperature, and a 
    // barometer (MPL3115A2) which can provide both barometric pressure and humidity.
    // Controllers for these are wrapped in a convenient plugin class:
    var weather = new Shield({
      variant: "ARDUINO", // or PHOTON
      freq: 1000,         // Set the callback frequency to 1-second
      elevation: 100      // Go to http://www.WhatIsMyElevation.com to get your current elevation
    });
    
    // The weather.on("data", callback) function invokes the anonymous callback function 
    // whenever the data from the sensor changes (no faster than every 25ms). The anonymous 
    // function is scoped to the object (e.g. this == the instance of Weather class object). 
    weather.on("data", function () {
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
      var message = new device.Message(payload);
      
      // For debugging purposes, write out the message payload to the console
      console.log("Sending message: " + message.getData());
      
      // Send the message to Azure IoT Hub
      client.sendEvent(message, printResultFor('send'));
    });
});
    
// Helper function to print results in the console
function printResultFor(op) {
  return function printResult(err, res) {
    if (err) console.log(op + ' error: ' + err.toString());
    if (res && (res.statusCode !== 204)) console.log(op + ' status: ' + res.statusCode + ' ' + res.statusMessage);
  };
}
{% endhighlight %}

In this code you do a number of things:

1. <code>board.on()</code> - This function triggers the board to invoke the anonymous callback function as soon as the board is on and ready. All of the application code for the device is written inside this callback function.
2. Define the <code>weather</code> object. This is a instance of the wrapper around the temperature and barometer on the weather shield (represented by the Shield object) connected to the board. When you instantiate the object, you can specify a frequency to collect the data from the sensors. Many sensors are capable of collecting data in fraction of a second intervals. You may not want to collect data and send it to your Azure IoT Hub that frequently. The <code>freq</code> property defines (in milliseconds) how often to raise an event to report the data from the sensor. In this example you are establishing the callback at a frequency of once per second for the <code>weather</code> object.
4. <code>message</code> is the object that represents the data you are sending to Azure IoT Hub. This is a JSON formatted message.

When <code>client.sendEvent()</code> is invoked, the JSON message is sent to Azure IoT Hub. For now, nothing happens with the message once it is received in your IoT Hub because you haven't set up anything that will capture the message and do something with it (we will get to that soon). By default, the messages have a one-day retention time.

You can view the file source file [here =>](https://github.com/ThingLabsIo/IoTLabs/tree/master/Azure).

## Run the App
When you run the application it will execute on your computer, and thanks to Johnny-Five, it will connect with your board and work directly with it. Basically, your computer is acting as a gateway - or hub - and communicating with the board as one of potentially many devices (or spokes). If you continue on past the intro labs, in a future lab you will deploy the Node.js application to another device (like a Raspberry Pi) which will act as the gateway and connect to multiple spoke devices.

<blockquote>
  PARTICLE PHOTON USERS: When you power on the Photon and it establishes a Wi-Fi connection, the first thing it does is a 'phone home' to the Particle Cloud where it registers itself as online. When it does that, it also registers its local IP address. When you run the Node.js application, thanks to the Johnny-Five framework and the Particle-IO plugin, the Node app pings the Particle Cloud and requests the IP address for the device name you specified (that is why the Particle Token and Device ID/Alias are needed). Once the application has the local IP address for the Photon, all communications with the device are over local TCP (which is why your development machine and the Photon have to be on the same network). Since the communication from the Node.js app to the Photon is over TCP and not USB, the Photon doesn't need to be plugged into your USB port - it simply needs to be powered on and on the same Wi-Fi you configured it for (and the machine running the Node.js app has to be on the same Wi-Fi network). 
</blockquote>

Open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following commands (replace c:\Development\IoTLabs with the path that leads to your labs folder):

<pre>
  cd C:\Development\IoTLabs
  node weather.js
</pre>

After the board initializes you will see messages printing out once per second. This is the message payload that is being sent to Azure IoT Hub.

<blockquote>
If you downloaded the [Device Explorer][deviceexplorer] utility for Windows you can open the _Data_ tab, select a device, and click _Monitor_ to begin monitoring messages as they come into your Azure IoT Hub.
</blockquote>

When you want to quite the application, press <kbd>CTRL</kbd> + <kbd>C</kbd> twice to exit the program without closing the window (you may also have to press <kbd>Enter</kbd>). 

<blockquote>
  PARTICLE PHOTON USERS: After stopping the application press the _Reset_ button on the Photon to prepare it for the next run.
</blockquote> 

## Conclusion &amp; Next Steps

In this lab you learned how to write a Node.js + Johnny-Five application that collects environment telemetry and sends it to Azure IoT Hub. In the [next lab][visualize-iot-with-powerbi] you will setup some Azure services to store and visualize the data.

[Next Lab ->][visualize-iot-with-powerbi]

{% include next-previous-post-in-category.html %}

[setup-azure-iot-hub]: ../setup-azure-iot-hub
[sending-telemetry]: ../sending-telemetry
[visualize-iot-with-powerbi]: ../visualize-iot-with-powerbi
[deviceexplorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md
