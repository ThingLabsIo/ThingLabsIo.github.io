---
layout: page-fullwidth
title: "Command &amp; Control"
subheadline: "Particle Photon IoT Lab 5"
teaser: "In this lab you will build a Photon circuit that acts as both a message sending device (an ambient light detector) and a command/control receiving device (LED). You will learn how to send command/control messages and how to process them. These are the concepts of connected Things."
show_meta: true
comments: true
header: no
breadcrumb: true
categories:
    - iot-photon-labs
    - connected-things-101
permalink: "/photon/05/"
---
If you haven't already done so, please follow the instructions in [Lab 00: Getting Started][1] section.

### Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will use a photoresistor to create an ambient light sensor. You will send data collected from the ambient light sensor to Nitrogen and process it to determine is the LED should be on or off. You will then send a message indicting the state of the LED after the command is processed. In this lab you are simulating two devices - an ambient light sensor and a light -- with a single Photon.
  
## Bill of Materials
What you will need (all the parts from the previous lessons):

1. [Particle Photon][2]
2. USB to micro-USB cable (there is one included in the [Photon Development Kit][2])
3. LED (there is one included in the [Photon Development Kit][2])
4. Two (2) 220-Ohm 1/4 Watt resistor (there are two included in the [Photon Development Kit][2])
5. Photoresistor (there is one included in the [Photon Development Kit][2])
7. [Jumper wires](https://www.sparkfun.com/products/12795)

For this lab series you are using a Particle Photon, a small Wi-Fi enabled development board. The Photon is an excellent prototyping board for connected Things and Particle, the makers of the Photon, sell the P0 chip that drives the board (so when you are ready to go into production you can easily use the same chip). While you can develop for the Photon using Particle Build or Particle Dev and leverage the Particle Cloud, this lab series is designed to teach you how to build a Wi-Fi based hub-and-spoke system, similar to how SmartThings or Phillips Hue works. For the first few labs you will learn how to create Node.js applications that run on your PC and control the Photon. Later in the lab you will learn how to deploy the Node.js applications to a hub device, like a Raspberry Pi 2 or an Arduino Y&uacute;n, which will act as the field gateway for all of the connected Photons in your solution.

## Wiring the Board
This lesson uses a similar wiring configuration from [Lab 3][10], so its possible that you already have most of the wiring done from the previous lesson. if not, wire the Photon according to the diagram.

<img src="/images/photon_lab03_bb.png"/>

### Resistors
One of the 220 Ohm resistor is one part of the voltage divider, working in partnership with the photoresistor.

The other 220 Ohm resistor connects to the negative (shorter) lead of the LED.

### Wires

1. Connect 3.3V to the positive side rail.
2. Connect GND to the negative side rail.
3. Connect the red/positive side rail to one end of the 220 Ohm resistor that will connect be part of the voltage divider.
4. Connect the other end of the 220 Ohm resistor to both one end of the photoresistor and to analog pin 0 (A0).
5. Connect the other end of the photoresistor to the negative side rail.
6. Connect digital pin _D0_ to the positive lead of the LED (the longer lead is the positive lead). NOTE: in [Lab 3][10] the LED was wired to _D7_ - since _D7_ is not e a PWM capable output, you must move the LED to another pin.
7. Connect the other lead from the LED to the negative siderail (opposite the wiring in steps 1-6).
8. Connect the other 220 Ohm resistor from the negative siderail to the _GND_ pin next to the _D7_ pin.

## Writing the Code
For this lab you will create a new file named <strong>lab05.js</strong> in the same directory as you did in the previous labs. There are no additional dependencies after [Lab 4][8], so we don't need to make any changes to the _package.json_ file.

In the lab05.js file start by declaring the key objects, including Johnny Five, Particle IO and Johnny Five photoresistor (this is where you will start to use the 'object model' that Johnny Five provides. You will also declare the Nitrogen objects that you will use in this application, including the service and lightSensor.

{% highlight javascript %}
// Define the Johnny Five and Particle-IO variables
var five = require("johnny-five"),
    board, photoresistor, led;
var particle = require("particle-io");
var Store = require("nitrogen-file-store"),
    nitrogen = require("nitrogen"),
    service, lightSensor;
{% endhighlight %}

Define the Nitrogen configuration settings. Replace _YOUR API KEY HERE_ with your actual API key.

{% highlight javascript %}
var config = {
    host: process.env.HOST_NAME || 'api.nitrogen.io',
    http_port: process.env.PORT || 443,
    protocol: process.env.PROTOCOL || 'https',
    api_key: process.env.API_KEY || 'YOUR API KEY HERE'
};
{% endhighlight %}

Instantiate the Nitrogen objects, including the <code>store</code>, <code>service</code>, and <code>lightSensor</code> device.

{% highlight javascript %}
config.store = new Store(config);
service = new nitrogen.Service(config);

// Create a new Nitrogen device for the photoresistor
// This device will send data it reads from a sensor
lightSensor = new nitrogen.Device({
    nickname: 'lab05_lightSensor',
    name: 'Lab 05 Light Sensor',
    tags: ['sends:_lightState', 'executes:_lightLevel']
});
{% endhighlight %}
    
The <code>tags</code> in this declaration indicate the two types of messages this device will deal with.

- <code>_lightState</code> is a message that the lightSensor device will send.
- <code>_lightLevel</code> is a message that the lightSensor device will execute

Of course, you don't need the Cloud to turn on and off an LED locally. For this example the light sensor device is actually playing the part of both a light sensor (the device that detects ambient light and reports the light level) and a light (or LED) which receives commands (on/off) and reports its state. With that in mind, the light sensor sends the <code>_lightLevel</code> command, the light/LED executes the <code>_lightLevel</code> command (e.g. reacts to it) and then sends a <code>_lightState</code> message.

Instantiate the Johnny Five <code>board</code> object.

{% highlight javascript %}
// Define the Johnny Five board as your Particle Photon
board = new five.Board({
  io: new particle({
    token: process.env.PARTICLE_KEY || 'YOUR API KEY HERE',
    deviceId: process.env.PARTICLE_DEVICE || 'YOUR DEVICE ID OR ALIAS HERE'
  })
});
{% endhighlight %}

Define a variable to represent the LED:

{% highlight javascript %}
var LEDPIN = "D0";
{% endhighlight %}

## Connect to Nitrogen
Prior to the <code>board.on()</code> function you typically create, you need to use the Nitrogen <code>service</code> object to connect to the Nitrogen service (<strong>api.nitrogen.io</strong>) using the <code>lightSensor</code> device context

{% highlight javascript %}
// Connect the lightSensor device defined above
// to the Nitrogen service instance.
service.connect(lightSensor, function(err, session, lightSensor) {
    if (err) { return console.log('Failed to connect lightSensor: ' + err); }
    
    // TODO: 00: Create an instance of the subclassed CommandManager object for the lightSensor
    
    // TODO 01: board.on() function goes here
    
});
{% endhighlight %}

## Create the Board.On() CallBack and Photoresistor Oject
Add the <code>board.on()</code> callback function and define the Johnny Five Sensor object for the photoresistor. The Johnny Five Sensor object (the variable you named <code>photoresistor</code>) has two properties you define - the pin to read data from (_A0_ or Analog 0) and the frequency (_freq_) to send the reading to a callback function (<code>Sensor.on()</code>). By passing <code>1000</code> to the frequency property you are instructing the Sensor to invoke the callback once per second. Add the following cod where you have the <code>// TODO 01: board.on() function goes here</code> comment.

{% highlight javascript %}
    // The board.on() executes the anonymous function when the 
    // Partile Photon reports back that it is initialized and ready.
    board.on("ready", function() {
        console.log("Board connected...");
    
        // Create a new `photoresistor` hardware instance.
        photoresistor = new five.Sensor({
            pin: 'A0',  // Analog pin 0
            freq: 1000  // Collect data once per second
        });
    
        // Define the LED object using the pin
        led = new five.Led(LEDPIN);
   
        // Inject the `sensor` hardware into the Repl instance's context;
        // Allows direct command line access
        board.repl.inject({
            pot: photoresistor,
            led:led
        });

        // TODO 02 - photoresistor.on() callback goes here
    });
{% endhighlight %}

### The Nitrogen CommandManager
Within the Nitrogen client library, the _CommandManager_ class provides the base infrastructure for command processing within Nitrogen. The CommandManager is responsible for watching the device's message stream and making state changes based on the relevant commands it receives. The CommandManager must be subclassed for each device type that it represents. Each subclass is responsible for providing the functionality of four (4) functions.

1. <code>isRelevant(message)</code> - should the subclass care about the message
2. <code>isCommand(message)</code> - should the subclass act on the command in the message
3. <code>obsoletes(downstreamMsg, upstreamMsg)</code> - should the subclass care anymore (e.g. has the command already been executed, is it stale)
4. <code>executeQueue()</code> - perform actions based on the commands deemed appropriate (i.e. 'active' commands)

Start by defining the CommandManager subclass and its constructor. Add the following code at the end of the lab05.js file (after the end of the <code>board.on()</code> function):

{% highlight javascript %}
// Create a command manager object and 
// set it's prototype to the generic nitrogen.CommandManager 
function LightManager() {
    nitrogen.CommandManager.apply(this, arguments);
}

LightManager.prototype = Object.create(nitrogen.CommandManager.prototype);
LightManager.prototype.constructor = LightManager;
{% endhighlight %} 

#### Override _isRelevant()_
<code>CommandManager.isRelevant(message)</code>

- <code>message</code>: _Object_, The message to test for relevance.

Within the subclass you have to override four functions - <code>isRelevant()</code> is the first of those. This function is responsible for looking at any incoming message and determining if the subclass should even care. For this demo you can assume that if the message is <code>_lightState</code> or <code>_lightLevel</code> that the subclass should consider it relevant, but only if it came from this device (i.e. don't consider messages from other devices relevant for now).

{% highlight javascript %}
// Override: CommandManager.isRelevant(message)
// Return true if this message is relevant to the CommandManager
// _lightState and _lightLevel are the messages the LightManager cares about
LightManager.prototype.isRelevant = function(message) {
    var relevant = ( (message.is('_lightState') || message.is('_lightLevel')) &&
                     (!this.device || message.from === this.device.id || message.to == this.device.id) );

    return relevant;
};
{% endhighlight %}

#### Override _isCommand()_
<code>CommandManager.isCommand(message)</code>

- <code>message</code>: _Object_, The message to test to see if it is a relevant command.

The subclass needs to determine if the message received is a command that needs executing. The <code>isCommand()</code> function is used to determne that. For this demo, the criteria is simple - only messages of the type <code>_lightLevel</code> need to be executed (remember the <code>tags</code> set on the <code>lightSensor</code> object?).

{% highlight javascript %}
// Override: CommandManager.isCommand(message)
// Return true if this message is a command that this
// CommandManager should process. 
LightManager.prototype.isCommand = function(message) {
    return message.is('_lightLevel');
};
{% endhighlight %}

#### Override _obsoletes()_
<code>CommandManager.obsoletes(downstreamMsg, upstreamMsg)</code>

- <code>downstreamMsg</code>: _Object_, The downstream message that potentially obsoletes the upstream message.
- <code>upstreamMsg</code>: _Object_, The upstream message that is potentially obsoleted by the downstream message.

The <code>obsoletes()</code> function is responsible for determining if an upstream message is still, or no longer, relevant. Messages become obsolete if they have already been executed but weren't cleared from the queue or if they are stale (i.e. a downstream message renders the upstream message in question out-of-date). For obsoletes, overrides should start their implementation by calling this function for base functionality. You should obsolete the upstream message if:

- The downstream message of the type <code>_lightState</code>, and
- The downstream message is a response to the upstream message, and
- The upstream message is of the type <code>_lightLevel</code>

If those statements all return true then the upstream message is no longer relevant and should be considered obsolete. 

{% highlight javascript %}
// Override: CommandManager.obsoletes(downstreamMsg, upstreamMsg)
// Returns true if the given message upstream 
// is obsoleted by the downstream message.
LightManager.prototype.obsoletes = function(downstreamMsg, upstreamMsg) {
    if (nitrogen.CommandManager.obsoletes(downstreamMsg, upstreamMsg))
        return true;

    var value = downstreamMsg.is('_lightState') &&
                downstreamMsg.isResponseTo(upstreamMsg) &&
                upstreamMsg.is('_lightLevel');

    return value;
};
{% endhighlight %}

#### Override _executeQueue()_
<code>CommandManager.executeQueue()</code>

The <code>executeQueue()</code> function is responsible for taking action on any active commands remaining (e.g. <code>isRelevant == true</code> and <code>isCommand == true</code> and <code>obsoletes == false</code>). In the executeQueue method you evaluate the <code>activeCommands</code> array and take appropriate action.

Start by validating that the device context is available and there is at least one active command.

{% highlight javascript %}
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
{% endhighlight %}

Continue by finding the final state that matters. In this lab you are taking in an ambient light reading from the lightSensor device and making a decision to turn an LED on or off. That means all that matters is the latest ambient light value (i.e. the most recent one). Depending on your needs in other use cases, you may want to do something with each active command, so in this lab you will use a <code>forEach</code> loop to iterate through all of the active commands.

{% highlight javascript %}
    var commandIds = [];
    var lightOn;

    // Find the final state and collect all the active command ids
    // You will use them in a moment.
    activeCommands.forEach(function(activeCommand) {
        // Collect active command IDs
        commandIds.push(activeCommand.id);
{% endhighlight %}

In the loop you will capture the ambientLight value that is defined in the message coming from the lightSensor (the <code>_lightLevel</code> message). You haven't written the code for the message yet, so you are actually setting the spec for the message here (i.e. you must remember what you call the active command here - in this case the command is named <code>ambientLight</code>). 

{% highlight javascript %}
        var light = activeCommand.body.command.ambientLight;
{% endhighlight %}

While in the loop, set the <code>ligthOn</code> value (true/false) based on a predefined ambientLight threshold (you should use the same threshold you used in [Lab 3][10]), and code the <code>forEach</code> loop.

{% highlight javascript %}
        // Determine the final state of the light (on/true or off/false)
        lightOn = light > 950;
    });
{% endhighlight %}

Now that you have iterated through the activeCommands you can set the state of the LED based on the last value you set <code>lightOn</code> to.

{% highlight javascript %}
    // Turn the light on or off based on final state
    if (led != null) { // Make sure the led sensor is available before using it
        if(lightOn) { 
           led.on();
        } else { 
           led.off();
        }
    }
    else {
        console.log("The 'led' object was null.");
    }
{% endhighlight %}

Now that the <code>executeQueue()</code> function has set the state of the LED you need to compose a response (downstream) message to send. The response is a <code>_lightState</code> message. The <code>command</code> in this message is simply the current <code>on</code> state of the LED (on: true/false). Notice that there is a <code>response_to</code> value in the message that contains all of the command IDs that this downstream message is in response to. This helps with the <code>obsoletes()</code> function as well as providing any message subscribers the information to correlate the response to one or more commands it is in response to.

{% highlight javascript %}
    // This is the response to the _lightLevel command.
    // Notice the response_to is the array of command ids from above. This is used in the obsoletes method above as well.
    var lightMessage = new nitrogen.Message({
        type: '_lightState',
        tags: nitrogen.CommandManager.commandTag(self.device.id),
        body: {
            command: {
                on: lightOn
            }
        },
        response_to: commandIds
    });
{% endhighlight %}

The last thing to do in the <code>executeQueue()</code> function is to send the response/downstream message. Rather than a simple <code>message.send(session)</code> call like in [Lab 4][8], this one will use the full <code>message.send(session, callback)</code> signature. In the callback you need to call <code>CommandManager.process(message)</code> which sets up the message stream to collapse and the message to expire, which drops it from the queue.

{% highlight javascript %}
    lightMessage.send(this.session, function(err, message) {
        if (err) return callback(err);
        
        console.log("Message sent: " + JSON.stringify(lightMessage));
        
        // let the command manager know we processed this _lightState message by passing it the _isOn message.
        self.process(new nitrogen.Message(lightMessage));

        // need to callback if there aren't any issues so commandManager can proceed.   
        return callback();
    });
};
{% endhighlight %}

#### Override _start()_
There is one additional function to override which I haven't mentioned until now because it only runs once once, when the session is started. That is <code>CommandManager.start(session, callback)</code>. This function, as the name implies, starts command processing on the message stream. It fetches all the current messages and starts execution. It also establishes a subscription to handle new messages as they are received. In the <code>start()</code> function you will define a <code>filter</code> that helps identify relevant messages (before <code>isRelevant</code>) to help reduce the noise coming into processing. For this lab you will simply set the filter to the device ID of the current context (the <code>lightSensor</code> device). This will scope message for evaluation to only those coming from this device. Obviously in production scenarios where you are working with multiple devices you would want a different filter.

{% highlight javascript %}
// Override: start
// Starts command processing on the message stream using the principal’s session. 
// It fetches all the current messages, processes them, and then starts execution.
// It also establishes a subscription to handle new messages and automatically executes 
// them as they are received.
LightManager.prototype.start = function(session, callback) {

    var filter = {
        tags: nitrogen.CommandManager.commandTag(this.device.id)
    };

    return nitrogen.CommandManager.prototype.start.call(this, session, filter, callback);
};    
{% endhighlight %}

### Add _session.connect()_
Go back up to where the <code>session.start()</code> function is defined. You should have the <code>// TODO: 00: Create an instance of the subclassed CommandManager object for the lightSensor</code> comment just prior to the <code>board.on()</code> definition. Add the following code to instantiate the LightManager object (which is a subclass of the CommandManager).

{% highlight javascript %}
    // Create an instance of the subclassed CommandManager object for the lightSensor
    new LightManager(lightSensor).start(session, function(err, message) { 
        if (err) return session.log.error(JSON.stringify(err)); 
    });
{% endhighlight %}

### Add _photoresistor.on()_
Inside the <code>board.on()</code> function you should have a <code>// TODO: photoresistor.on() goes here</code> comment to add the <code>photoresistor.on()</code> function. Add the following code in that location.
<pre>
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
                tags: nitrogen.CommandManager.commandTag(lightSensor.id),
                body: {
                    command: {
                        ambientLight: lightLevel
                    }
                },
                to: lightSensor.id
            });
            
            // Send the message
            ambientLightMessage.send(session);
            
            console.log("Message sent: " + JSON.stringify(ambientLightMessage));
        });
</pre>

This is similar to the Nitrogen message you created in [Lab 4][8], with some differences. First, notice the <code>type</code> is set to <code>_lightLevel</code>. This is the message type that you have defined as containing a command to be executed. That means when this message is received by any subscribing device (this same device in this case) the command will be processed and executed as necessary. (As a note, it is by convention to use an underscore (\_) as the prefix for custom types - types defined by Nitrogen don't use underscores). 

Secondly, notice that the <code>body</code> of the message is defined as a <code>command</code> named <code>ambientLight</code> (this is the command name you used in the <code>executeQueue()</code> function). The value being passed in with that command is the captured <code>lightLevel</code>.

Finally, notice the <code>to</code> property of the message. This is used to define the device (by ID) that this message is intended for (in this case, the same device).

If you want to compare your code to the final solution you can see the code in GitHub [here][11].

## Run the App
To run the application, plug the Photon into a power supply using the USB cable. Open a terminal window (Mac OS) or Node.js command prompt (Windows) and execute the following commands (replace _C:\Development\IoTLabs_ with the path that leads to your labs folder):
<pre>
cd C:\Development\IoTLabs 
node lab05.js
</pre>

After a few initialization messages you will see upstream and downstream messages start to go by. You should also see the LED turn on if there is not enough ambient light (you can cover and uncover it to see the state changes). Remember, when you defined the Johnny-Five <code>photoresistor</code> object, you defined a 1000 millisecond (1 second) interval for capturing the sensor reading and sending the Nitrogen message. Couple the one-second interval with Cloud latency and you may see a perceived delay of a couple seconds before the LED state changes as the result of ambient light changes. 

<pre>
C:\Development\IoTLabs>node lab05.js
1431508763751 Device(s) particle-io
1431508764610 Connected particle-io
5/13/2015 2:18:31 AM: Light Sensor: debug: session: created.
5/13/2015 2:18:31 AM: Light Sensor: debug: CommandManager::execute: empty command queue.
5/13/2015 2:18:31 AM: Light Sensor: debug: starting heartbeat interval
5/13/2015 2:18:31 AM: Light Sensor: info: commandManager: started.
5/13/2015 2:18:32 AM: Light Sensor: debug: session: socket.io connected
1431508715226 Repl Initialized 
>> Board connected...
Message sent: {"ts":"2015-05-13T09:18:36.255Z","body":{"command":{"ambientLight":588}},"type":"_lightLevel","tags":"command:55530d233959110100402b46","to":"55530d233959110100402b46"}
Message sent: {"ts":"2015-05-13T09:18:37.258Z","body":{"command":{"ambientLight":597}},"type":"_lightLevel","tags":"command:55530d233959110100402b46","to":"55530d233959110100402b46"}
Message sent: {"ts":"2015-05-13T09:18:37.086Z","body":{"command":{"on":true}},"type":"_lightState","tags":"command:55530d233959110100402b46","response_to":["555316ece683b8010082da29"]}
Message sent: {"ts":"2015-05-13T09:18:38.261Z","body":{"command":{"ambientLight":198}},"type":"_lightLevel","tags":"command:55530d233959110100402b46","to":"55530d233959110100402b46"}
Message sent: {"ts":"2015-05-13T09:18:37.926Z","body":{"command":{"on":true}},"type":"_lightState","tags":"command:55530d233959110100402b46","response_to":["555316ece683b8010082da29"]}
Message sent: {"ts":"2015-05-13T09:18:39.261Z","body":{"command":{"ambientLight":75}},"type":"_lightLevel","tags":"command:55530d233959110100402b46","to":"55530d233959110100402b46"}
Message sent: {"ts":"2015-05-13T09:18:38.664Z","body":{"command":{"on":true}},"type":"_lightState","tags":"command:55530d233959110100402b46","response_to":["555316ed89361e01007d0faa"]}
Message sent: {"ts":"2015-05-13T09:18:39.442Z","body":{"command":{"on":true}},"type":"_lightState","tags":"command:55530d233959110100402b46","response_to":["555316ed89361e01007d0faa"]}
Message sent: {"ts":"2015-05-13T09:18:40.261Z","body":{"command":{"ambientLight":74}},"type":"_lightLevel","tags":"command:55530d233959110100402b46","to":"55530d233959110100402b46"}
Message sent: {"ts":"2015-05-13T09:18:40.212Z","body":{"command":{"on":false}},"type":"_lightState","tags":"command:55530d233959110100402b46","response_to":["555316ee58b8db0100554679"]}
Message sent: {"ts":"2015-05-13T09:18:41.262Z","body":{"command":{"ambientLight":57}},"type":"_lightLevel","tags":"command:55530d233959110100402b46","to":"55530d233959110100402b46"}
Message sent: {"ts":"2015-05-13T09:18:40.967Z","body":{"command":{"on":false}},"type":"_lightState","tags":"command:55530d233959110100402b46","response_to":["555316ee58b8db0100554679","555316ef58b8db01005546a5"]}
Message sent: {"ts":"2015-05-13T09:18:42.265Z","body":{"command":{"ambientLight":587}},"type":"_lightLevel","tags":"command:55530d233959110100402b46","to":"55530d233959110100402b46"}
Message sent: {"ts":"2015-05-13T09:18:41.676Z","body":{"command":{"on":false}},"type":"_lightState","tags":"command:55530d233959110100402b46","response_to":["555316ee58b8db0100554679","555316ef58b8db01005546a5"]}
Message sent: {"ts":"2015-05-13T09:18:42.345Z","body":{"command":{"on":false}},"type":"_lightState","tags":"command:55530d233959110100402b46","response_to":["555316f074a17d01000dd3db","555316f185d14e0100c8b8c0"]}
Message sent: {"ts":"2015-05-13T09:18:43.266Z","body":{"command":{"ambientLight":590}},"type":"_lightLevel","tags":"command:55530d233959110100402b46","to":"55530d233959110100402b46"}
Message sent: {"ts":"2015-05-13T09:18:43.221Z","body":{"command":{"on":true}},"type":"_lightState","tags":"command:55530d233959110100402b46","response_to":["555316f074a17d01000dd3db","555316f185d14e0100c8b8c0","555316f2e683b8010082da3f"]}
Message sent: {"ts":"2015-05-13T09:18:44.268Z","body":{"command":{"ambientLight":592}},"type":"_lightLevel","tags":"command:55530d233959110100402b46","to":"55530d233959110100402b46"}
</pre>

Press <kbd>CTRL</kbd> + <kbd>C</kbd> twice then <kbd>Enter</kbd>to exit the program without closing the window.

## Conclusion &amp; Next Steps
Congratulations! You have created a _Thing_ that collects input from its physical environment and sends it to the Cloud, and then received command/control messages and changes its state accordingly. You can use these concepts to make any number of Connected Things. In the [next lab][9] you will learn how to connect multiple devices so that you can have one physical device alter the state of another physical device.

[Next Lab ->][9]

{% include next-previous-post-in-category.html %}

 [1]: /photon/00/
 [2]: https://store.particle.io/?product=photon-kit
 [8]: /photon/04/
 [9]: /photon/06/
 [10]: /photon/03/
 [11]: https://github.com/ThingLabsIo/IoTLabs/blob/master/Photon/Labs05/lab05.js