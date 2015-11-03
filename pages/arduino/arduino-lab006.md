---
layout: page-fullwidth
comments: true
show_meta: true
title: "Sending Data to the Cloud"
subheadline: "Arduino + Azure IoT Lab 6"
teaser: "In this lab you will build a simple ambient light detection app and send the data that is being collected to the Cloud."
show_meta: true
comments: true
header: no
breadcrumb: true
categories:
    - iot-arduino-labs
    - connected-things-101
permalink: "/arduino/06/"
---
If you haven't already done so, please follow the instructions in [Lab 00: Getting Started](../00/) section.

### Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will build a simple ambient light detection app (similar to [Lab 03](../03/)) and send the data that is being 
collected to Azure IoT Hub. In following labs you will build a data pipeline to process the incoming data stream and output it
to a visualization tool.

## Bill of Materials
What you will need (all the parts from [Lab 02][2]:

1. [Arduino Uno][uno] or [Arduino Y&uacute;n][yun] 
2. USB to micro-USB cable
3. [5mm Green LED](http://www.sparkfun.com/products/12062)
4. [330-Ohm 1/4 Watt resistor](http://www.sparkfun.com/products/10969) (Orange-Orange-Brown)
5. [Photoresistor (5528)](http://www.sparkfun.com/products/9088)
6. [10k-Ohm 1/4 Watt resistor](http://www.sparkfun.com/products/10969) (Brown-Black-Orange)

## Wiring the Board
This lab follows the same wiring plan as [Lab 04](../04/). If your board is still wired up from [Lab 04](../04/), you can leave 
it as is and go to the next step. If not, wire it as follows.

<img src="/images/Lab03_bb.png"/>

### Resistors
The 10k Ohm resistor is one part of the voltage divider, working in partnership with the photoresistor.

The 330 Ohm resistor connects to the negative (shorter) lead of the LED.

### Wires
This is where building a habit of connecting positive (5V) and negative (GND) pins from the Arduino to the breadboard side-rails starts to pay off. In this lab the pin coming from GND to the negative side-rail supports both the photoresistor circuit and the LED circuit.

1. Connect 5V to the positive side rail.
2. Connect GND to the negative side rail.
3. Connect the red/positive side rail to one end of the 10k resistor.
4. Connect the other end of the 10k Ohm resistor to both one end of the photoresistor and to analog pin 0 (A0).
5. Connect the other end of the photoresistor to the negative side rail.
6. Connect digital pin 13 to the positive lead of the LED (the longer lead is the positive lead).
7. Connect the other lead from the LED to the 330 Ohm resistor.
8. Connect the other end of the 330 Ohm resistor to the negative side rail.

## Write the Code
Since you are using Node.js for this lab you can take advantage of the dependency management capabilities that Node.js and NPM provide. 
You need to let your application know that it has a dependency on the __Azure IoT Device__ package in addition to the __Johnny-Five__ package. 
In Node.js this is done with the _package.json_ file. This file provides some basic meta-data about the application, including any dependencies 
on packages that can be retrieved using NPM (according to [npmjs.com](https://www.npmjs.com) today, NPM stands for Narrating Prophetic 
Monks...not Node Package Manager like you may have thought).

Using your favorite/preferred text/code editor, create a file in your development directory named __package.json__ and add the following:

{% highlight javascript %}
{
  "name": "IoT-Labs",
  "version": "0.1.0",
  "private":true,
  "description": "Sample app that connects a device to Azure using Node.js",
  "main": "lab06.js",
    "author": "YOUR NAME HERE",
  "license": "MIT",
  "dependencies": {
    "johnny-five": "^0.8.104",
    "azure-iot-device": "^1.0.0-preview.3"
  }
}
{% endhighlight %}

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

Create another file in the same directory named __lab06.js__ and add the following code:

{% highlight javascript %}
'use strict';
// Define the Johnny Five, Particle and Azure IoT objects
var five = require ("johnny-five");
var device = require('azure-iot-device');

// Create a Johnny-Five board instance to represent your Particle Photon
// Board is simply an abstraction of the physical hardware, whether is is an 
// Arduino, Raspberry Pi, Particle Photon, or other boards.
var board = new five.Board();
var LEDPIN = 11;
var ANALOGPIN = 0;

var deviceId = process.env.DEVICE_NAME || 'YOUR AZURE GATEWAY DEVICE NAME HERE (E.G. RICKGRIMES-HUB)';
var location = process.env.DEVICE_LOCATION || 'THE LOCATION OF THE DEVICE (E.G. HOME OFFICE)';
var connectionString = process.env.IOTHUB_CONN || 'YOUR AZURE IOT HUB DEVICE-SPECIFIC CONNECTION STRING HERE';

// Create an Azure IoT client that will manage the connection to your IoT Hub
// The client is created in the context of an Azure IoT device, which is why
// you use a device-specific connection string.
var client = new device.Client(connectionString, new device.Https());
{% endhighlight %}

In this code you define four variables that you will be working with:

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
following code to _lab06.js_:

{% highlight javascript %}
board.on("ready", function() {
  console.log("Board connected...");
  
  // Set pin 11 to PWM mode - See Lab 04 for more information on PWM
  this.pinMode(LEDPIN, five.Pin.PWM);
    
  // Create a new 'photoresistor' hardware instance.
  var photoresistor = new five.Sensor({
    pin: ANALOGPIN,  // Analog pin 0
    freq: 1000 // Set the callback frequency to 1-second
  });
  
  // Define the callback function for the photoresistor reading.
  // The Sensor class raises the "data" event every 25ms by default.
  photoresistor.scale(0, 255).on("data", function() {
    var darkIntensity = this.value;
    
    // Write the value to the PWM output pin
    // As the detected light intensity decreases (it gets darker)
    // the value coming in on pin A0 increase.
    // Using the value as output will make the LED grow brighter
    // as the room gets darker.
    board.analogWrite(LEDPIN, this.value);
    
    // TODO: Create and send a message to Azure IoT
    
  });
});
{% endhighlight %}

The __scale(0, 255)__ function call will result in the value being read from pin _A0_ - which has a value range of 0-1023 - to be scaled down to 
a value range of 0-255. The LED has a value range of 0-255, which means that the scaling matches the value range for the PWM output.

## Send Telemetry to Azure IoT Hubs
Now that you have the device working, using the _Sensor_ class (the <code>photoresistor</code> object) and the _data_ event, you need to create 
and send a message to Azure. The __Message__ class enables you to define a telemetry message with a JSON payload. Then, using the _client_ object
you can invoke __sendEvent(message, callback)__ function to send the message over HTTP to the Azure IoT Hub.

Replace the <code>TODO: Create and send a message to Azure IoT</code> with the following code.

{% highlight javascript %}
      var payload = JSON.stringify({
        deviceId: deviceId,
        location: location,
        messurementType: 'darkness',
        messurementValue: darkIntensity
      });
      
      // Create the message based on the payload JSON
      var message = new device.Message(payload);
      // For debugging purposes, write out the message payload to the console
      console.log("Sending message: " + message.getData());
      // Send the message to Azure IoT Hub
      client.sendEvent(message, printResultFor('send'));
{% endhighlight %}

When <code>client.sendEvent()</code> is invoked, the JSON message is sent to Azure IoT Hub. For now, nothing happens with the message once it is 
received in your IoT Hub because you haven't set up anything that will capture the message and do something with it (we will get to that soon). 
By default, the messages have a one-day retention time.

Beforw you run the app, you need to add the __printResultFor()__ callback function you reference in the <code>client.sendEvent()</code> function. 
Add the following to the end of the _lab06.js_ file, after the <code>board.on()</code> function.

{% highlight javascript %}
// Helper function to print results in the console
function printResultFor(op) {
  return function printResult(err, res) {
    if (err) console.log(op + ' error: ' + err.toString());
    if (res && (res.statusCode !== 204)) console.log(op + ' status: ' + res.statusCode + ' ' + res.statusMessage);
  };
}
{% endhighlight %}

If you want to compare your code to the final solution you can see the code in GitHub [here](https://github.com/ThingLabsIo/IoTLabs/blob/master/Arduino/Lab05_07/lab06.js).

## Run the App and Verify Data is Being Sent
Open a terminal window (Mac OS) or Node.js command prompt (Windows) and execute the following commands (replace _C:\Development\IoTLabs_ with the 
path that leads to your labs folder):

<pre>
cd C:\Development\IoTLabs 
node lab06.js
</pre>

After the board initializes you will see messages printing out in the console once per second. This is the message payload that is being sent to 
Azure IoT Hub.

<blockquote>
  If you downloaded the [Device Explorer[deviceexplorer] utility for Windows in [Lab 1](../01/) you can open the _Data_ tab, select a device, 
  and click _Monitor_ to begin monitoring messages as they come into your Azure IoT Hub.
</blockquote>

When you want to quite the application, press <kbd>CTRL</kbd> + <kbd>C</kbd> twice to exit the program without closing the window (you may also have to press <kbd>Enter</kbd>). After stopping the application press the _Reset_ button on the Photon to prepare it for the next run. 

## Conclusions &amp; Next Steps
Congratulations! You have created your first internet connected Thing. Welcome to the world of IoT. In the [next lab][nextlab] you will setup some Azure services to store and visualize the data.

[Next Lab ->][nextlab]

## Want to Try Something?
If you want to experiment with more labs like this you can try out these code samples:

1. [Temperature Device][6]

{% include next-previous-post-in-category.html %}

[nextlab]: /arduino/07/
[uno]: http://www.arduino.cc/en/Main/ArduinoBoardUno
[yun]: http://www.arduino.cc/en/Main/ArduinoBoardYun
[1]: /arduino/00/
[2]: /arduino/02/
[3]: /arduino/05/
[4]: /arduino/01/
[5]: /arduino/03/
[6]: https://github.com/ThingLabsIo/IoTLabs/blob/master/Arduino/Labs04_06/lab04_temp.js