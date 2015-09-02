---
layout: page-fullwidth
title: "Connecting Multiple Things"
subheadline: "Particle Photon IoT Lab 6"
teaser: "In this lab you will build two Things, and enable one Thing to share its state with the other, which will alter its own state based on the information received. In other words, you will build one connected Thing that controls another Connected Thing."
show_meta: true
comments: true
header: no
breadcrumb: true
categories:
    - iot-photon-labs
    - connected-things-101
permalink: "/photon/06/"
---
If you haven't already done so, please follow the instructions in [Lab 00: Getting Started][lab00] section.

### Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will use two Photons to create a connection between two devices. The concepts are similar to the previous lab, although you will learn about Permissions to enable two or more devices to engage in communication. The devices you will build are the same as in [Lab 01][lab01] and [Lab 02][lab02].
  
## Bill of Materials
What you will need (all the parts from the previous lessons):

1. [Particle Photon][photon] x2
2. USB to micro-USB cable (there is one included in the [Photon Development Kit][photon]) x2
3. LED (there is one included in the [Photon Development Kit][photon])
4. Two (2) 220-Ohm 1/4 Watt resistor (there are two included in the [Photon Development Kit][photon])
5. Photoresistor (there is one included in the [Photon Development Kit][photon])
7. [Jumper wires](https://www.sparkfun.com/products/12795)

For this lab series you are using a Particle Photon, a small Wi-Fi enabled development board. The Photon is an excellent prototyping board for connected Things and Particle, the makers of the Photon, sell the P0 chip that drives the board (so when you are ready to go into production you can easily use the same chip). While you can develop for the Photon using Particle Build or Particle Dev and leverage the Particle Cloud, this lab series is designed to teach you how to build a Wi-Fi based hub-and-spoke system, similar to how SmartThings or Phillips Hue works. For the first few labs you will learn how to create Node.js applications that run on your PC and control the Photon. Later in the lab you will learn how to deploy the Node.js applications to a hub device, like a Raspberry Pi 2 or an Arduino Y&uacute;n, which will act as the field gateway for all of the connected Photons in your solution.

## Wiring the Indicator Light Photon
This lesson uses a similar wiring configuration from [Lab 5][lab05], but it is spread across two Photons - onw with the photoresistor and one with the LED. Wire the first Photon according to the following diagram.

<img src="/images/photon_lab06a_bb.png"/>

## Wiring the Ambient Light Sensor Board
The second Photon uses the same wiring configuration for the ambient light sensor device from [Lab 4][lab04]. Wire the second Photon according to the following diagram:
<img src="/images/photon_lab06b_bb.png"/>

## Writing the Light Sensor Code
The ambient light sensor device code is very similar to the code you wrote in [Lab 04][lab04]. Create a new file in the same directory as the other labs and name it <strong>lab06_lightSensor.js</strong>. Following is the code for that file.

{% highlight javascript %}
// Define the Jonny Five and Spark-IO variables
var five = require ("johnny-five"),
    board, photoresistor;
var Spark = require("spark-io");
var Store = require("nitrogen-file-store"),
    nitrogen = require("nitrogen"),
    service, lightSensor;

var config = {
    host: process.env.HOST_NAME || 'api.nitrogen.io',
    http_port: process.env.PORT || 443,
    protocol: process.env.PROTOCOL || 'https',
    api_key: process.env.API_KEY || 'YOUR API KEY HERE'
};
config.store = new Store(config);
service = new nitrogen.Service(config);

// Create a new Nitrogen device for the photoresistor
// This device will send data it reads from a sensor
lightSensor = new nitrogen.Device({
    nickname: 'lab06_lightSensor',
    name: 'Lab 06 Light Sensor',
    tags: ['sends:_lightLevel']
});

// Define the Johnny Five board as your Particle Photon
board = new five.Board({
  io: new Spark({
    token: process.env.PARTICLE_KEY || 'YOUR API KEY HERE',
    deviceId: process.env.PARTICLE_DEVICE || 'YOUR DEVICE ID OR ALIAS HERE'
  })
});

// Connect the lightSensor device defined above
// to the Nitrogen service instance.
service.connect(lightSensor, function(err, session, lightSensor) {
    if (err) { return console.log('Failed to connect lightSensor: ' + err); }
    
    board.on("ready", function() {
        console.log("Board connected...");
    
        // Create a new `photoresistor` hardware instance.
        photoresistor = new five.Sensor({
            pin: 'A0',  // Analog pin 0
            freq: 1000  // Collect data once per second
        });

        // Inject the `sensor` hardware into the Repl instance's context;
        // Allows direct command line access
        board.repl.inject({
            pot: photoresistor
        });
        
        // Define the event handler for the photo resistor reading
        // The freq value used when the photoresistor was defined
        // determines how often this is invoked, thus controlling
        // the frequency of Nitrogen messages.
        photoresistor.on('data', function() {
            // Capture the ambient light level from the photo resistor
            var lightLevel = this.value;
            
            // Create a Nitrogen Message to send the _lightLevel
            var ambientLightMessage = new nitrogen.Message({
                type: '_lightLevel',
                // Specify a command tag that you can scope to
                // This will enable you to filter out non-relevant messages
                tags: nitrogen.CommandManager.commandTag('lab06'),
                body: {
                    command: {
                        ambientLight: lightLevel
                    }
                }
            });
            
            // Send the message
            ambientLightMessage.send(session);
            // Show the message in the console
            console.log("Message sent: " + JSON.stringify(ambientLightMessage));
        });
    });
});
{% endhighlight %}

You should notice two differences.

1. The definition of the <code>lightSensor</code> Nitrogen device includes a <code>tags</code> parameter specifying that this device only sends <code>_lightLevel</code> messages, and doesn't execute any commands.
2. The <code>tags</code>property of the <code>ambientLightMessage</code> specifies <code>lab06</code> instead of <code>this.device.id</code>. This enables you to set a scoping property (e.g. tag devices for an application). Any device listening to the messaage stream can filter to only the messages with this tag rather than to a single device ID.  

For many IoT scenarios you will create devices that simply send telemetry to Cloud services, and possibly receive command and control messages back (similar to [Lab 05][lab05]). You won't have a need to have one device control another. In this lab you are designing the ambient light sensor to send telemetry to the Cloud without any knowledge of any users or devices that may be listening to the message stream. Next you will create a status indicator device the listens to the message stream of the ambient light device and uses the LED to indicate the light level that was detected. 

If you want to compare your code to the final solution you can see the code in GitHub [here][https://github.com/ThingLabsIo/IoTLabs/blob/master/Photon/Lab06/lab06_lightSensor.js].

## Writing the Code for the Indicator Light Device
The indicaor light device is a status indicator using a  LED connected to pin _D0_ and plus GND. The indicator light will illuminate with intensity inversely relative to the amount of ambient light reported. The biggest difference between this and the previous labs is that these are two physically separated boards that will communicate with each other via a Cloud service (Nitrogen). Create a new file in the same directory as the other labs and name it <strong>lab06_light.js</strong>. Following is the code for that file.

{% highlight javascript %}
// Define the Jonny Five and Spark-IO variables
var five = require ("johnny-five"),
    board, led;
var Spark = require("spark-io");
var Store = require("nitrogen-file-store"),
    nitrogen = require("nitrogen"),
    service, indicatorLight;

var config = {
    host: process.env.HOST_NAME || 'api.nitrogen.io',
    http_port: process.env.PORT || 443,
    protocol: process.env.PROTOCOL || 'https',
    api_key: process.env.API_KEY || 'YOUR API KEY HERE'
};
config.store = new Store(config);
service = new nitrogen.Service(config);

// Create a new Nitrogen device for the photoresistor
// This device will send data it reads from a sensor
indicatorLight = new nitrogen.Device({
    nickname: 'lab06_indicatorLight',
    name: 'Lab 06 Indicator Light',
    tags: ['sends:_intensity', 'executes:_lightLevel']
});

// Define the Johnny Five board as your Particle Photon
board = new five.Board({
  io: new Spark({
    token: process.env.PARTICLE_KEY || 'YOUR API KEY HERE',
    deviceId: process.env.PARTICLE_DEVICE || 'YOUR DEVICE ID OR ALIAS HERE'
  })
});

var LEDPIN = "D0";

// Define a command tag that you can scope to.
// This will enable you to filter to only relevant messages
var cmdTag = 'lab06';

// Connect the indicatorLight device defined above
// to the Nitrogen service instance.
service.connect(indicatorLight, function(err, session, indicatorLight) {
    if (err) { return console.log('Failed to connect lab06_indicatorLight: ' + err); }
    
    // Create an instance of the subclassed CommandManager object for the indicatorLight
    new LightManager(indicatorLight).start(session, function(err, message) { 
        if (err) return session.log.error(JSON.stringify(err)); 
    });
        
    board.on("ready", function() {
        console.log("Board connected...");
           
        // Initialize the LED
        led = new five.Led(LEDPIN);
   
        // Inject the `sensor` hardware into the Repl instance's context;
        // Allows direct command line access
        board.repl.inject({
            led:led
        });
    });
});

// Create a command manager object and 
// set it's prototype to the generic nitrogen.CommandManager 
function LightManager() {
    nitrogen.CommandManager.apply(this, arguments);
}

LightManager.prototype = Object.create(nitrogen.CommandManager.prototype);
LightManager.prototype.constructor = LightManager;

// Override: CommandManager.isRelevant(message)
// Return true if this message is relevant to the CommandManager
// _color and _lightLevel are the messages the LightManager cares about
LightManager.prototype.isRelevant = function(message) {
    return (message.is('_intensity') || message.is('_lightLevel'));
};

// Override: CommandManager.isCommand(message)
// Return true if this message is a command that this
// CommandManager should process. 
LightManager.prototype.isCommand = function(message) {
    return message.is('_lightLevel')
};

// Override: CommandManager.obsoletes(downstreamMsg, upstreamMsg)
// Returns true if the given message upstream 
// is obsoleted by the downstream message.
LightManager.prototype.obsoletes = function(downstreamMsg, upstreamMsg) {
    if (nitrogen.CommandManager.obsoletes(downstreamMsg, upstreamMsg))
        return true;

    var value = downstreamMsg.is('_intensity') &&
                downstreamMsg.isResponseTo(upstreamMsg) &&
                upstreamMsg.is('_lightLevel');

    return value;
};

// Override: CommandManager.executeQueue()
// Executes the active commands in the message queue
LightManager.prototype.executeQueue = function(callback) {
    if (!this.device) return callback(new Error('no device attached to control manager.'));

    var self = this;

    // CommandManager.activeCommands()
    // Returns the array of commands that are currently active for this manager
    var activeCommands = this.activeCommands();

    // Verify there are activeCommands
    if (activeCommands.length === 0) {
        this.session.log.warn('LightManager::executeQueue: no active commands to execute.');
        return callback();
    }
    
    var commandIds = []; // An array to collect Command IDs
    var brightness = 0; // Variable for the LED brightness value

    // Find the final state and collect all the active command ids
    // You will use them in a moment.
    activeCommands.forEach(function(activeCommand) {
        // Collect active command IDs
        commandIds.push(activeCommand.id);
        // Collect the ambient light level from the message
        var light = activeCommand.body.command.ambientLight;

        // Set the brightness value based on the ambient light level.
        // In bright light it will have no brightness, and in dim light 
        // it will have the most brightness.
        // Increase the brightness value for ambient light levels above 950
        brightness = map(light, 950, 1023, 0, 255);
        
        // Constrain the values to eliminate negative values and values beyond the upper bound
        brightness = constrain(brightness, 0, 255);
    });
    
    // If the LED is present, set its brightness value
    if(led != null) {
        led.brightness(brightness);
    }
    
    // This is the response to the _lightLevel command.
    // Notice the response_to is the array of command ids from above. This is used in the obsoletes method above as well.
    var lightMessage = new nitrogen.Message({
        type: '_intensity',
        tags: nitrogen.CommandManager.commandTag(cmdTag),
        body: {
            command: {
                intensity: brightness
            }
        },
        response_to: commandIds
    });
    
    lightMessage.send(this.session, function(err, message) {
        if (err) return callback(err);
        
        console.log("Message sent: " + JSON.stringify(message));
        
        // let the command manager know we processed this message.
        self.process(new nitrogen.Message(message));

        // need to callback if there aren't any issues so commandManager can proceed.   
        return callback();
    });
};

// Override: start
// Starts command processing on the message stream using the principal’s session. 
// It fetches all the current messages, processes them, and then starts execution.
// It also establishes a subscription to handle new messages and automatically executes 
// them as they are received.
LightManager.prototype.start = function(session, callback) {

    var filter = {
        tags: nitrogen.CommandManager.commandTag(cmdTag)
    };
    
    return nitrogen.CommandManager.prototype.start.call(this, session, filter, callback);
};

// This function maps a value from one range into another range
// Example: map (25, 0, 25, 0, 50) returns 50
// Example: map (20, 0, 100, 0, 10) returns 2
function map(x, in_min, in_max, out_min, out_max) {
  return Math.round((x - in_min) * (out_max - out_min) / (in_max - in_min) + out_min);
}

// This function ensures a value is within a defined range
function constrain(x, in_min, in_max) {
  return Math.round(x < in_min ? in_min : x > in_max ? in_max : x);
}
{% endhighlight %}

The code for the indicator light should be fairly self explanatory if you have done the previous labs. The biggest difference is the use of the <code>five.Led</code> object instead of controlling a pin. Other than that the use of a sub-classed CommandManager (the <code>LightManager</code>) is basically the same as [Lab05][lab05]. The new concept that you need to understand to enable distinctly different devices to communicate with each other is _Permissions_.

If you want to compare your code to the final solution you can see the code in GitHub [here](https://github.com/ThingLabsIo/IoTLabs/blob/master/Photon/Labs06/lab06_light.js).

## Permissions
Permissions in Nitrogen are pretty much what they sound like. Permissions enable you to define what Nitrogen Principals (users, devices, applications, etc.) can do in relation to other Nitrogen Principals. This could include enabling a user to have admin privelages over one or more devices, or giveing a group of devices to the ability to subscribe to the message stream from another device, etc. Permissions are set using the Nitrogen command-line.

For this lab you will give permission to the indicator light device to subscribe to the ambient light device (these devices must be in the same Nitrgen account). This means that the ambient light device doesn't need to know anything about the indicator light device, but the indicator light device will know everything that the ambient light device sends on its message stream. To set the permission you first need to geth the device IDs that Nitrogen is using for each of these devices. To do that, run each of the applications, which will dynamically provision the devices in Nitrogen.

Open a terminal window (Mac OS) or Node.js command prompt (Windows) and execute the following commands (replace _C:\Development\IoTLabs_ with the path that leads to your labs folder):

<pre>
cd C:\Development\IoTLabs 
node lab06_light.js
</pre>

Press <kbd>CTRL</kbd> + <kbd>C</kbd> twice then <kbd>Enter</kbd>to exit the program without closing the window.

Next run the lightSensor app - execute the following commands (replace _C:\Development\IoTLabs_ with the path that leads to your labs folder):

<pre>
cd C:\Development\IoTLabs 
node lab06_lightSensor.js
</pre>

Press <kbd>CTRL</kbd> + <kbd>C</kbd> twice then <kbd>Enter</kbd>to exit the program without closing the window.

Once the devices have been provisioned in Nitrogen you can create a Permission using the device IDs. You can get the device IDs by executing the following command in terminal/Node.js command prompt.

<pre>
n2 device ls
</pre>

You should see something similar to the following (depending on what devices you have):

<pre>
C:\Development\IoTLabs>n2 device ls
ID                        TYPE    NAME                     LAST IP          LAST CONNECTION
555cabbb1bd28701000c95ec  device  Lab 06 Light Sensor      24.248.182.246   2015-05-20T16:37:51.064Z
555cabbe1bd28701000c9633  device  Lab 06 Indicator Light   24.248.182.246   2015-05-20T16:37:51.020Z    
</pre>

Using the values in the ID column for the appropriate devices, execute a command in the following format (this only needs to be done once):

<pre>
    n2 permission add --action subscribe --authorized true --issuedTo YOUR INDICATOR LIGHT DEVICE ID HERE --principalFor YOUR AMBIENT LIGHT DEVICE ID HERE
</pre>

This command is telling Nitrogen to create a permission that allows the indicator light device to subscribe to the message stream from the ambient light device. There are five (5) options for the type of permission (the <code>action</code>) that can be created.

1. __(empty)__: issuedTo is authorized to perform all actions on principalFor.
2. __admin__: issuedTo is authorized to perform administrative operations on principalFor.
3. __send__: issuedTo is authorized to send messages to principalFor.
4. __subscribe__: issuedTo is authorized to subscribe to messages from principalFor.
5. __view__: issuedTo is authorized to see principalFor in searches.

You can also use <code>authorized</code> argument to either give or deny the specified permission (e.g. deny a device permission to subscribe to another device).

## Run the Apps
Now that you have the permission in place you can run both apps and see the indicator light device change state based on the telemetry sent by the ambient light device. Open a terminal window (Mac OS) or Node.js command prompt (Windows) and execute the following commands (replace _C:\Development\IoTLabs_ with the path that leads to your labs folder):

<pre>
cd C:\Development\IoTLabs 
node lab06_light.js
</pre>

Open another terminal window or Node.js command prompt and execute the following commands (replace _C:\Development\IoTLabs_ with the path that leads to your labs folder):
<pre>
cd C:\Development\IoTLabs 
node lab06_lightSensor.js
</pre>

Play around with changing the amount of light the ambient light sensor reports. You should see the indicator light change state, although there will be some latency in the response because the ambient light sensor is reading data in one-second intervals, and the impact of network latency.

For each device, press <kbd>CTRL</kbd> + <kbd>C</kbd> twice then <kbd>Enter</kbd>to exit the program without closing the window.

## Conclusion &amp; Next Steps
Congratulations! You hace not created two connected Things and caused one of them to change state based on the telemetry (in the form of a message stream) of another device. Through these labs you have now been exposed to most of the concepts you need to build connected Things. In the next lab you will learn how to pull the data you are collecting from your Things and visualize it in an application.

 [photon]: https://store.particle.io/?product=photon-kit
 [lab00]: /photon/00/
 [lab01]: /photon/01/
 [lab02]: /photon/02/
 [lab04]: /photon/04/
 [lab05]: /photon/05/