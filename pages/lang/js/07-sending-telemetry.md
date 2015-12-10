---
layout: page-js
title: "Sending Telemetry to the Cloud"
subheadline: "Connected Thing Labs using Azure and JavaScript"
teaser: "In this lab you will gather telemetry and send it to the cloud."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [azure, azure-iot-hub, javascript, node.js, johnny-five, arduino, photon]
permalink: /lang/js/sending-telemetry/
---
# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will write a Node.js application that runs on a hub (your development machine) and collects data from a development board and sends it up to your Azure IoT Hub.

# Bill of Materials
What you will need:

1. One of the following development boards:
  * [SparkFun RedBoard Programmed with Arduino](https://www.sparkfun.com/products/12757)
  * [Arduino Uno R3](https://www.sparkfun.com/products/11021)
  * [Arduino Y&uacute;n](https://www.sparkfun.com/products/12053)
  * [Particle Photon Development Kit](https://store.particle.io/?product=photon-kit)
2. USB cable to connect the board to your computer (the type will vary depending on the board you are using)
3. [5mm Green LED](http://www.sparkfun.com/products/12062)
4. [330 Ohm 1/4 Watt resistor](http://www.sparkfun.com/products/10969) (Orange-Orange-Brown)
5. [Photoresistor (5528)](http://www.sparkfun.com/products/9088)
6. [10k Ohm 1/4 Watt resistor](http://www.sparkfun.com/products/10969) (Brown-Black-Orange)

For Arduino and RedBoard, you must upload the Standard Firmatta to the board. See [Setting Up Your Arduino Firmware](/device/arduino/setup-arduino) for details. 

For Particle Photon, you must upload the VoodooSpark firmware to the Photon. See [Setting Up Your Particle Photon Firmware](/device/photon/setup-photon) for details.

# Wiring the Board
This lab follows the same wiring plan as the ['Reading Analog Input' lab](/lang/js/reading-analog-input/) . If your board is still wired up from the ['Input Controls Output'](/lang/js/input-controls-output/) lab, you can leave it as is and go to the next step. If not, wire it as follows.

<div id="wiring-tabs">
  <ul>
    <li><a href="#arduino"><span>Arduino</span></a></li>
    <li><a href="#photon"><span>Photon</span></a></li>
  </ul>
  <div id="arduino">
    <img src="/images/Lab02_bb.png"/>
  </div>
  <div id="photon">
    <img src="/images/photon_lab02_bb.png"/>
  </div>
</div>

<script>
$( "#wiring-tabs" ).tabs();
</script>

### Resistors
The 10k Ohm resistor is one part of the voltage divider, working in partnership with the photoresistor.

The 330 Ohm resistor connects to the negative (shorter) lead of the LED.

### Wires
This is where building a habit of connecting positive (5V) and negative (GND) pins from the Arduino to the breadboard side-rails starts to pay off. In this lab the pin coming from GND to the negative side-rail supports both the photoresistor circuit and the LED circuit.

1. Connect 3.3V to the positive side rail.
2. Connect GND to the negative side rail.
3. Connect the red/positive side rail to one end of the 10k Ohm resistor.
4. Connect the other end of the 10k Ohm resistor to both one end of the photoresistor and to analog pin 0 (A0).
5. Connect the other end of the photoresistor to the negative side rail.
6. Connect digital pin 13 (or D1 for the Photon) to the positive lead of the LED (the longer lead is the positive lead).
7. Connect the other lead from the LED to the 330 Ohm resistor.
8. Connect the other end of the 330 Ohm resistor to the negative side rail.

# Write the Code
Since you are using Node.js for this lab you can take advantage of the dependency management capabilities that Node.js and NPM provide. 
You need to let your application know that it has a dependency on the __Azure IoT Device__ package in addition to the __Johnny-Five__ package. 
In Node.js this is done with the _package.json_ file. This file provides some basic meta-data about the application, including any dependencies 
on packages that can be retrieved using NPM (according to [npmjs.com](https://www.npmjs.com) today, NPM stands for Narrating Prophetic 
Monks...not Node Package Manager like you may have thought).

Using your favorite/preferred text/code editor, create a file in your development directory named __package.json__ and add the following:

<div id="json-tabs">
  <ul>
    <li><a href="#arduino"><span>Arduino</span></a></li>
    <li><a href="#photon"><span>Photon</span></a></li>
  </ul>
  <div id="arduino">
    {% highlight json %}
    {
      "name": "IoT-Labs-Arduino",
      "repository": {
        "type": "git",
        "url": "https://github.com/ThingLabsIo/IoTLabs/Arduino"
      },
      "version": "0.1.0",
      "private":true,
      "description": "Sample app that connects a device to Azure using Node.js",
      "main": "nightlight.js",
        "author": "YOUR NAME",
      "license": "MIT",
      "dependencies": {
        "johnny-five": "latest",
        "azure-iot-device": "latest"
      }
    }
    {% endhighlight %}
  </div>
  <div id="photon">
    {% highlight json %}
    {
      "name": "IoT-Labs-Photon",
      "repository": {
        "type": "git",
        "url": "https://github.com/ThingLabsIo/IoTLabs/Photon"
      },
      "version": "0.1.0",
      "private":true,
      "description": "Sample app that connects a device to Azure using Node.js",
      "main": "nightlight.js",
        "author": "YOUR NAME",
      "license": "MIT",
      "dependencies": {
        "johnny-five": "latest",
        "particle-io": "latest",
        "azure-iot-device": "latest"
      }
    }
    {% endhighlight %}
  </div>
</div>

<script>
$( "#json-tabs" ).tabs();
</script>

With the package.json file created you can use NPM to pull down the necessary Node modules. Open a terminal window (Mac OS X) or Node.js 
command prompt (Windows) and execute the following commands (replace _C:\Development\IoTLabs_ with the path that leads to your development directory):

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

Next you will create the application code to gather ambient light data and send it to the cloud.

Create another file in the same directory named __nightlight.js__ and add the following code:

<div id="code-tabs">
  <ul>
    <li><a href="#arduino"><span>Arduino</span></a></li>
    <li><a href="#photon"><span>Photon</span></a></li>
  </ul>
  <div id="arduino">
    {% highlight javascript %}
    'use strict'
    // https://github.com/ThingLabsIo/IoTLabs/blob/master/Arduino/nightlight.js
    // Define the Johnny Five object
    var five = require ("johnny-five"); 
    var device = require('azure-iot-device');

    var location = process.env.DEVICE_LOCATION || 'GIVE A NAME TO THE LOCATION OF THE THING';
    var connectionString = process.env.IOTHUB_CONN || 'YOUR IOT HUB DEVICE-SPECIFIC CONNECTION STRING HERE';
    
    // Create an Azure IoT client that will manage the connection to your IoT Hub
    // The client is created in the context of an Azure IoT device, which is why
    // you use a device-specific connection string.
    var client = device.Client.fromConnectionString(connectionString);
    var deviceId = device.ConnectionString.parse(connectionString).DeviceId;

    // Define the pin that is connected to the LED
    var LEDPIN = 13;
    // Define the pin you will use to read the residual voltage 
    // coming from the photoresistor
    var ANALOGPIN = 0;
    var darkIntensity = 0;
    
    // Create a Johnny Five board instance to represent your board.
    // Board is simply an abstraction of the physical hardware, whether it is 
    // a Photon, Arduino, Raspberry Pi or other boards. 
    var board = new five.Board();
    
    /*
    // You may optionally specify the port by providing it as a property
    // of the options object parameter. * Denotes system specific 
    // enumeration value (ie. a number)
    // OSX
    new five.Board({ port: "/dev/tty.usbmodem****" });
    // Linux
    new five.Board({ port: "/dev/ttyUSB*" });
    // Windows
    new five.Board({ port: "COM*" });
    */
    {% endhighlight %}
  </div>
  <div id="photon">
    {% highlight javascript %}
    'use strict'
    // https://github.com/ThingLabsIo/IoTLabs/blob/master/Photon/nightlight.js
    // Define the Johnny Five and Particle-IO variables
    var five = require ("johnny-five"); 
    var Particle = require("particle-io");
    var device = require('azure-iot-device');
    
    var location = process.env.DEVICE_LOCATION || 'GIVE A NAME TO THE LOCATION OF THE THING';
    var connectionString = process.env.IOTHUB_CONN || 'YOUR IOT HUB DEVICE-SPECIFIC CONNECTION STRING HERE';
    
    // Create an Azure IoT client that will manage the connection to your IoT Hub
    // The client is created in the context of an Azure IoT device, which is why
    // you use a device-specific connection string.
    var client = device.Client.fromConnectionString(connectionString);
    var deviceId = device.ConnectionString.parse(connectionString).DeviceId;

    // Set up the access credentials for Particle and Azure 
    var token = process.env.PARTICLE_KEY || 'YOUR PARTICLE ACCESS TOKEN HERE'; 
    var deviceId = process.env.PHOTON_ID || 'YOUR PARTICLE PHOTON DEVICE ID/ALIAS HERE'; 
    
    // Define the pin that is connected to the LED
    var LEDPIN = "D0";
    // Define the pin you will use to read the residual voltage 
    // coming from the photoresistor
    var ANALOGPIN = "A0";
    var darkIntensity = 0;
    
    // Create a Johnny Five board instance to represent your Particle Photon.
    // Board is simply an abstraction of the physical hardware, whether it is 
    // a Photon, Arduino, Raspberry Pi or other boards. 
    var board = new five.Board({ 
      io: new Particle({ 
        token: token, 
        deviceId: deviceId 
      }) 
    });
    {% endhighlight %}
  </div>
</div>

<script>
$( "#code-tabs" ).tabs();
</script>

In this code you define four key variables that you will be working with:

1. <code>five</code> - represents the Johnny-Five framework capabilities, which provide a type of object model for working with boards like Arduino and Particle.
2. <code>device</code> - represents the Azure IoT Hubs SDK.
3. <code>board</code> - represents the physical board you are using.
5. <code>client</code> - the agent class that facilitates the communication between your device and the Azure IoT Hub. It takes the device-specific connection string as an argument and establishes a connection with the IoT Hub.

Now that the objects are created, you can get to the meat of the application. Johnny-Five provides a board 'ready' event that makes a callback 
when the board is on, initialized and ready for action. Inside the anonymous callback function is where your application code executes (this 
function is invoked when the board is ready for use).

Johnny-Five provides a collection of objects that represent the board, the pins on the board, and various types of sensors and devices that could be 
connected to the board. The __Sensor__ class in Johnny-Five enables you to represent an analog sensor, such as a photoresistor.

Next you will use the _Sensor_ class to represent the photoresistor and capture the light/darkness measurement. Start by defining 
a _photoresistor_ variable using the __Sensor__ class. Next create a handler function for the photoresistor _data_ event. Add the 
following code to _nightlight.js_:

{% highlight javascript %}
// The board.on() executes the anonymous function when the 
// board reports back that it is initialized and ready.
board.on("ready", function() {
  console.log("Board connected...");
  
  // Set pin connected to the LED to PWM mode
  this.pinMode(LEDPIN, five.Pin.PWM);
    
  // Create a new 'photoresistor' hardware instance.
  var photoresistor = new five.Sensor({
    pin: ANALOGPIN,
  });
    
  // The photoresistor.on("data", callback) function invokes the anonymous callback function 
  // whenever the data from the sensor changes every 25ms. The anonymous function is scoped 
  // to the object (e.g. this == the instance of Sensor class object). 
  // The scale(min, max) function scales the value to the defined range.
  photoresistor.scale(0, 255).on("data", function() {
    // Assign the value from the voltage divider to the darkIntensity variable 
    darkIntensity = this.value;
      
    // Write the value to the PWM output pin
    // As the detected light intensity decreases (it gets darker)
    // the value coming in on pin A0 increase.
    // Using the value as output will make the LED grow brighter
    // as the room gets darker.
    board.analogWrite(LEDPIN, this.value);
  });
  
  // TODO: Create and send a message to Azure IoT
  
});
{% endhighlight %}

The __scale(0, 255)__ function call will result in the value being read from pin _A0_ - which has a value range of 0-1023 - to be scaled down to 
a value range of 0-255. The LED has a value range of 0-255, which means that the scaling matches the value range for the PWM output.

## Send Telemetry to Azure IoT Hubs
Now that you have the device working you need to create a function that sends a message to your Azure IoT Hub once per second. The __Message__ class enables you to define a telemetry message with a JSON payload. Then, using the _client_ object
you can invoke the __sendEvent(message, callback)__ function to send the message over HTTP to the Azure IoT Hub.

Replace the <code>TODO: Create and send a message to Azure IoT</code> with the following code.

{% highlight javascript %}
// Execute a function once per second that will create send a new Azure IoT message with
// the device information and the darkness value.
setInterval(function(){
  var payload = JSON.stringify({
    deviceId: deviceId,
    location: location,
    messurementType: 'darkness',
    messurementValue: darkIntensity
  });
  
  // Create the message based on the payload JSON string
  var message = new device.Message(payload);
  // For debugging purposes, write out the message payload to the console
  console.log("Sending message: " + message.getData());
  // Send the message to Azure IoT Hub
  client.sendEvent(message, printResultFor('send'));
}, 1000);
{% endhighlight %}

When <code>client.sendEvent()</code> is invoked, the JSON message is sent to Azure IoT Hub. For now, nothing happens with the message once it is 
received in your IoT Hub because you haven't set up anything that will capture the message and do something with it (we will get to that soon). 
By default, the messages have a one-day retention time.

Beforw you run the app, you need to add the __printResultFor()__ callback function you reference in the <code>client.sendEvent()</code> function. 
Add the following to the end of the _nightlight.js_ file, __after__ the cosing of the <code>board.on()</code> function.

{% highlight javascript %}
// Helper function to print results in the console
function printResultFor(op) {
  return function printResult(err, res) {
    if (err) console.log(op + ' error: ' + err.toString());
    if (res && (res.statusCode !== 204)) console.log(op + ' status: ' + res.statusCode + ' ' + res.statusMessage);
  };
}
{% endhighlight %}

## Run the App and Verify Data is Being Sent
Open a terminal window (Mac OS) or Node.js command prompt (Windows) and execute the following commands (replace _C:\Development\IoTLabs_ with the 
path that leads to your labs folder):

<pre>
cd C:\Development\IoTLabs 
node nightlight.js
</pre>

After the board initializes you will see messages printing out in the console once per second. This is the message payload that is being sent to 
Azure IoT Hub.

If you downloaded the [Device Explorer][deviceexplorer] utility for Windows in ['Setting Up Azure IoT Hub'][setup-azure-iot-hub] you can open the _Data_ tab, select a device, and click _Monitor_ to begin monitoring messages as they come into your Azure IoT Hub.

When you want to quite the application, press <kbd>CTRL</kbd> + <kbd>C</kbd> twice to exit the program without closing the window (you may also have to press <kbd>Enter</kbd>). 

<blockquote>
  PARTICLE PHOTON USERS: After stopping the application press the _Reset_ button on the Photon to prepare it for the next run.
</blockquote>

## Conclusions &amp; Next Steps
Congratulations! You have created your first internet connected Thing. Welcome to the world of IoT. In the [next lab][visualize-iot-with-powerbi] you will setup some Azure services to store and visualize the data.

<a class="radius button small" href="{{ site.url }}/lang/js/visualize-iot-with-powerbi/">Go to 'Visualize IoT Data with Microsoft Power BI' ›</a>

[setup-azure-iot-hub]: ../setup-azure-iot-hub/
[sending-telemetry]: ../sending-telemetry/
[visualize-iot-with-powerbi]: ../visualize-iot-with-powerbi/
[deviceexplorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md
