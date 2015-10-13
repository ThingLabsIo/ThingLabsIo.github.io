---
layout: page-fullwidth
title: "Sending Telemetry to the Cloud"
subheadline: "Microsoft Azure IoT Lab 2"
teaser: "In this lab you will gather telemetry and send it to the cloud."
show_meta: true
comments: true
header: no
breadcrumb: true
categories:
    - iot-photon-labs
    - maker-101
author: "Doug Seven"
permalink: "/azure/02/"
---
### Table of Contents
*  Auto generated table of contents
{:toc}

If you haven't already done so, please follow the instructions in [Lab 00: Getting Started](/azure/00/) section.

In this lab you will write a Node.js application that runs on a hub (your development machine) and collects data from a Particle Photon (over local TCP) and sends it up to your Azure IoT Hub.

## Bill of Materials
What you will need:

1. [Particle Photon][1]
2. USB to micro-USB cable (there is one included in the [Photon Development Kit][1])
3. [SparkFun Weather Shield for Particle Photon - $32.95](https://www.sparkfun.com/products/13630)

In the previous lab you claimed your Particle Photon and prepared it by flashing it with the VoodooSpark firmware. Then you provisioned a new Azure IoT Hub and used either the _DeviceExploer_ or _iothub-explorer_ to provision a device. In this lab you will write a Node.js application that communicates with the Photon over local TCP and gathers the data from the temperature and barometer on the weather shield. Once you understand this concept you can apply it to any sensor that collects data, such as a light sensor, motion sensor, vibration or tilt sensors, and more.

## Write the Code
Since you will be using Node.js for this lab you can take advantage of the dependency management capabilities that Node.js and NPM provide. You need to let your application know that it has a dependency on the following NPM packages - Azure IoT Device, Johnny-Five and Particle-IO. In Node.js this is done with a _package.json_ file. This file provides some basic meta-data about the application, including any dependencies on packages that can be retrieved using NPM (according to [npmjs.com](https://www.npmjs.com) today, NPM stands for Narrating Prophetic Monks...not Node Package Manager like you may have thought).

Using your favorite/preferred text/code editor, create a file in your development directory named __package.json__ and add the following:

{% highlight javascript %}
{
  "name": "IoT-Labs",
  "version": "0.1.0",
  "private":true,
  "description": "Sample app that connects a device to Azure using Node.js",
  "main": "weather.js",
    "author": "YOUR NAME HERE",
  "license": "MIT",
  "dependencies": {
    "azure-iot-device": "^1.0.0-preview.3",
    "johnny-five": "^0.8.98",
    "particle-io": "^0.10.1",
    "j5-sparkfun-weather-shield": "^0.2.0"
  }
}
{% endhighlight %}

With the package.json file created you can use NPM to pull down the necessary Node modules. Open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following commands (replace _C:\Development\IoTLabs_ with the path that leads to your development directory):

On Windows, open the Node.js command prompt and type the following:
<pre>
  cd C:\Development\IoTLabs
  npm install
</pre>

On Mac OS X open Terminal and type the following:
<pre>
  cd ~/Development/IoTLabs
  sudo npm install
</pre>

Next you will create the application code to gather temperature, humidity, and barometer data and send it to the cloud.

Create another file in the same directory named __weather.js__.

The first thing you need to do is define the objects you will be working with in the application. The three things that matter are the Azure IoT Device object, a Johnny-Five framework object, the Weather Shield plugin object, and an object to represent the Photon. In order to complete this step, you will need the device ID you copied earlier when you were claiming the Photon (or the name/alias you gave the Photon when you updated the firmware to VoodooSpark) and your Particle Cloud access token. To get the access token, open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following command (you may be prompted to login or provide your Particle Cloud password again):

<pre>
  particle token list
</pre>

Find the token for _user_ (make sure if you see more than one that you choose the one that is not expired).

Optionally you can use a browser to navigate to [Particle Build](https://build.particle.io/) and find your Access Token on the setting page (click on the gear icon in the lower-left part of the screen).

Now add the following code to the __weather.js__ file:

{% highlight javascript %}
'use strict';
// Define the Jonny Five, Particle and Azure IoT objects
var five = require ("johnny-five");
var Weather = require("j5-sparkfun-weather-shield")(five);
var device = require('azure-iot-device');
var Particle = require("particle-io");

// Set up the access credentials for Particle and Azure
var token = process.env.PARTICLE_KEY || 'YOUR PARTICLE ACCESS TOKEN HERE';
var deviceId = process.env.DEVICE_NAME || 'YOUR PARTICLE PHOTON DEVICE ID/ALIAS HERE';
var location = process.env.DEVICE_LOCATION || 'THE LOCATION OF THE PARTICLE PHOTON DEVICE';
var connectionString = process.env.IOTHUB_CONN || 'YOUR IOT HUB DEVICE-SPECIFIC CONNECTION STRING HERE';

// Create a Johnny-Five board instance to represent your Particle Photon
// Board is simply an abstraction of the physical hardware, whether is is a 
// Photon, Arduino, Raspberry Pi or other boards.
var board = new five.Board({
  io: new Particle({
    token: token,
    deviceId: deviceId
  })
});

// Create an Azure IoT client that will manage the connection to your IoT Hub
// The client is created in the context of an Azure IoT device, which is why
// you use a device-specific connection string.
var client = new device.Client(connectionString, new device.Https());
{% endhighlight %}

In this code you define four variables that you will be working with:

1. <code>five</code> - represents the Johnny-Five framework capabilities, which provide a type of object model for working with boards like Arduino and Particle.
2. <code>Weather</code> - represents the Weather Shield plugin, which is compatible with both the Arduino and Photon variants.
3. <code>Particle</code> - represents the Particle IO framework capabilities which exposes the specifics of the Particle boards including the Core and the Photon.
4. <code>board</code> - a representation of the physical board you are using. This is created by specifying the Particle board and passing in the Particle Access Token and the device ID or alias.
5. <code>client</code> - This is the agent class that facilitates the communication between your device and the Azure IoT Hub. It takes the device-specific connection string as an argument and establishes a connection with the IoT Hub.

Now that the objects are created, you can get to the meat of the application. Johnny-Five provides a board 'ready' event that makes a callback when the board is on, initialized and ready for action. Inside the anonymous callback function is where your application code executes (this function is invoked when the board is ready for use).

Johnny-Five provides a collection of objects that represent the board, the pins on the board, and various types of sensors and devices that could be connected to the board. In this lab series you will work with the Temperature and Barometer classes as a representation of the HTU21D humidity sensor and the MPL3115A2 barometric pressure sensor respectively, however the sensor controller classes will be wrapped in the Weather class.

In the following code you will invoke the <code>board.on()</code> function which establishes a callback function that is invoked when the board is on, initialized and ready. All of the operational code for the board will be in the <code>board.on()</code> function (helper functions may exist outside the scope on the <code>board.on()</code> function). Within the <code>board.on()</code> function you will create an object reference to the sensors on the weather shield. Similar to the <code>board</code> object, the object you create to reference the shield will have an <code>on()</code> function that establishes a callback that exposes the data read from the sensors on the shield.

{% highlight javascript %}
// The board.on() executes the anonymous function when the 
// board reports back that it is initialized and ready.
board.on("ready", function() {
  console.log("Board connected...");
  // The SparkFun Weather Shield for the Particle Photon has two sensors on the I2C bus - 
  // a humidity sensor (HTU21D) which can provide both humidity and temperature, and a 
  // barometer (MPL3115A2) which can provide both barometric pressure and humidity.
  // Controllers for these are wrapped in the convenient `Weather` plugin class:
  var weather = new Weather({
    variant: "PHOTON"
  });

  var defaults = {
    deviceId: deviceId,
    location: location
  };

  // The weather.on("data", callback) function invokes the anonymous callback function 
  // whenever the data from the sensor changes (no faster than every 25ms). The anonymous 
  // function is scoped to the object (e.g. this == the instance of Weather class object). 
  weather.on("data", function() {
    var payload = Object.assign({}, defaults, {
      // celsius & fahrenheit are averages taken from both sensors
      celsius: this.celsius,
      fahrenheit: this.fahrenheit,
      pressure: this.pressure,
      feet: this.feet,
      meters: this.meters,
      relativeHumidity: this.relativeHumidity,
    });

    // Create the message based on the payload JSON
    var message = new device.Message(payload);
    // For debugging purposes, write out the message paylod to the console
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

1. <code>board.on()</code> - This function triggers the Photon to invoke the anonymous callback function as soon as the board is on and ready. All of the application code for the device is written inside this callback function.
2. Define the <code>weather</code> object. This is a representation of the two physical sensors on the shield connected to the Photon. Many sensors are capable of collecting data in fraction of a second intervals. You may not want to collect data and send it to your Azure IoT Hub that frequently. The <code>freq</code> property defines (in milliseconds) how often to raise an event to report the data from the sensor. In this example you are establishing the callback at the default rate of once per 25ms. 
3. <code>weather.on("data", ...)</code> creates an event handler to process the present state of the sensors connected to the board. In this case, processing involves creating a <code>payload</code> object containing telemetry data to the Azure IoT Hub.
4. <code>message</code> is the object that represents the data you are sending to Azure IoT Hub. This is a JSON formatted message.

When <code>client.sendEvent()</code> is invoked, the JSON message is sent to Azure IoT Hub. For now, nothing happens with the message once it is received in your IoT Hub because you haven't set up anything that will capture the message and do something with it (we will get to that soon). By default, the messages have a one-day retention time.

## Run the App
When you run the application it will execute on your computer, and thanks to Johnny-Five, it will connect with your Photon and work directly with it. Basically, your computer is acting as a hub and communicating via TCP over your local Wi-Fi network with the Photon as one of potentially many devices (or spokes). If you continue on past today, in a future lab you will deploy the Node.js application to another device (like a Raspberry Pi) which will act as the hub and connect to multiple spoke devices.

<blockquote>
  When you power on the Photon and it establishes a Wi-Fi connection, the first thing it does is a 'phone home' to the Particle Cloud where it registers itself as online. When it does that, it also registers its local IP address. When you run the Node.js application, thanks to the Johnny-Five framework and the Particle-IO plugin, the Node app pings the Particle Cloud and requests the IP address for the device name you specified (that is why the Particle Token and Device ID/Alias are needed). Once the application has the local IP address for the Photon, all communications with the device are over local TCP (which is why your development machine and the Photon have to be on the same network). Since the communication from the Node.js app to the Photon is over TCP and not USB, the Photon doesn't need to be plugged into your USB port - it simply needs to be powered on and on the same Wi-Fi you configured it for (and the machine running the Node.js app has to be on the same Wi-F network). 
</blockquote>

Open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following commands (replace c:\Development\IoTLabs with the path that leads to your labs folder):

<pre>
  cd C:\Development\IoTLabs
  node weather.js
</pre>

After the board initializes you will see messages printing out once per 10-seconds (this first message will print out after the initial 10-seconds). This is the message payload that is being sent to Azure IoT Hub.

<blockquote>
If you downloaded the Device Explorer utility for Windows you can open the _Data_ tab, select a device, and click _Monitor_ to begin monitoring messages as they come into your Azure IoT Hub.
</blockquote>

When you want to quite the application, press <kbd>CTRL</kbd> + <kbd>C</kbd> twice to exit the program without closing the window (you may also have to press <kbd>Enter</kbd>). After stopping the application press the _Reset_ button on the Photon to prepare it for the next run. 

## Conclusion &amp; Next Steps

In this lab you learned how to write a Node.js/Johnny-Five application that collects environment telemetry and sends it to Azure IoT Hub. In the [next lab][2] you will setup some Azure services to store and visualize the data.

[Next Lab ->][2]

{% include next-previous-post-in-category.html %}

[1]: https://store.particle.io/?product=photon-kit
[2]: /azure/03/
[deviceexplorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md
