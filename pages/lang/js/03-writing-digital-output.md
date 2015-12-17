---
layout: page-js
title: "Writing Digital Output"
subheadline: "Building Connected Things with JavaScript"
teaser: "In this lab you will use one of the digital output pins to send a signal to an LED."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [arduino, photon, iot, maker, javascript, node.js, johnny-five]
permalink: /lang/js/writing-digital-output/
---
If you haven't already done so, please follow the instructions in the ['Getting Started' lab]({ site.url }}/lang/js/getting-started/) section.

# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will use one of the digital output pins to send a signal to an LED. You will use digital pin D7, which also has an LED on the board connected to it (so you will be able to see it turn on and off even if you mis-wire something). While this lesson could be done using only the onboard LED, one of the objectives of this lab is to familiarize you with connecting input and output devices (an LED is an output device) to the board and controlling them with software.

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

For Arduino and RedBoard, you must upload the Standard Firmatta to the board. See [Setting Up Your Arduino Firmware]({ site.url }}/lang/js/setup-arduino/) for details. 

For Particle Photon, you must upload the VoodooSpark firmware to the board. See [Setting Up Your Particle Photon Firmware]({ site.url }}/lang/js/setup-photon/) for details.

# Wiring the Board
Next you need to wire up the Photon board so that it can send <code>ON</code> and <code>OFF</code> commands to the LED. You can wire your board according to the diagram (wire colors don't matter, but help with identification of purpose).

<div id="wiring-tabs">
  <ul>
    <li><a href="#arduino"><span>Arduino</span></a></li>
    <li><a href="#photon"><span>Photon</span></a></li>
  </ul>
  <div id="arduino">
    <img src="/images/lab01_bb.png" />
  </div>
  <div id="photon">
    <img src="/images/photon_lab01_bb.png" />
  </div>
</div>

<script>
$( "#wiring-tabs" ).tabs();
</script>

## LED
Insert a LED into the breadboard as shown in the diagram, connecting the positive lead to the D7 pin and inserting the negative lead into the negative (-) side rail. For reference, an LED has one lead that is longer than the other. The longer lead is the positive (+) lead, and the shorter lead is the negative (-) lead.

## Resistor
Connect a 220 Ohm resistor from the negative (-) side rail to the GND pin.

## Write the Code
Since we are using Node.js and Johnny-Five for this lab we can take advantage of the dependency management capabilities that Node.js provides. We need to let our application know that it has a dependency on the Johnny-Five framework as well as the Spark-IO framework so that when the application is prepared for execution, it can fetch the required dependencies for us. In Node.js this is done with a package.json file. This file provides some basic meta-data about the application, including any dependencies on packages that can be retrieved using NPM (according to [npmjs.com](https://www.npmjs.com) today, NPM stands for Narrating Prophetic Monks...not Node Package Manager like you may have thought).

Using your favorite/preferred text/code editor, create a file in your labs folder named <strong>package.json</strong> and add the following:

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
      "main": "blinky.js",
        "author": "YOUR NAME",
      "license": "MIT",
      "dependencies": {
        "johnny-five": "latest"
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
      "main": "blinky.js",
        "author": "YOUR NAME",
      "license": "MIT",
      "dependencies": {
        "johnny-five": "latest",
        "particle-io": "latest"
      }
    }
    {% endhighlight %}
  </div>
</div>

<script>
$( "#json-tabs" ).tabs();
</script>


With the package.json file created you can use NPM to pull down the necessary Node modules. Open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following commands (replace _C:\Development\IoTLabs_ with the path that leads to your labs folder):

<pre>
  cd C:\Development\IoTLabs
  npm install
</pre>

Next you will create the application code to make the LED turn on and off.

Create another file in the same directory named <strong>blinky.js</strong>.

The first thing you need to do is define the objects you will be working with in the application. The three things that matter are a Johnny-Five framework object, an object to represent the board, and the output pin the LED will be connected to. 

<blockquote>
  __PHOTON ONLY: In order to complete this step you will need the device ID you copied earlier when you were claiming the Photon (or the name/alias you gave the Photon when you updated the firmware to VoodooSpark) and your Particle Cloud access token. To get the access token, Open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following command (you may be prompted to login or provide your Particle Cloud password again):
  
  <pre>
    particle token list
  </pre>
  
  Find the token for _user_ (make sure if you see more than one that you choose the one that is not expired).
</blockquote>

Now add the following code to the <b>blinky.js</b> file:

<div id="code-tabs">
  <ul>
    <li><a href="#arduino"><span>Arduino</span></a></li>
    <li><a href="#photon"><span>Photon</span></a></li>
  </ul>
  <div id="arduino">
    {% highlight javascript %}
    'use strict'
    // Define the Johnny Five and Particle-IO variables
    var five = require ("johnny-five"); 

    // Define the pin that is connected to the LED 
    var LEDPIN = 13;
    
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
    // Define the Johnny Five and Particle-IO variables
    var five = require ("johnny-five"); 
    var Particle = require("particle-io");
    
    // Set up the access credentials for Particle and Azure 
    var token = process.env.PARTICLE_KEY || 'YOUR PARTICLE ACCESS TOKEN HERE'; 
    var deviceId = process.env.PHOTON_ID || 'YOUR PARTICLE PHOTON DEVICE ID/ALIAS HERE'; 
    
    // Define the pin that is connected to the LED 
    var LEDPIN = 'D7';
    
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

In this code you define four variables that you will be working with:

1. <code>five</code> - represents the Johnny Five framework capabilities, which provide a type of object model for working with boards like Arduino and Particle.
2. <code>Particle</code> - represents the Particle IO framework capabilities which exposes the specifics of the Particle boards inclusing the Core and the Photon.
3. <code>LEDPIN</code> - a variable that references pin D7, which you will connected the LED to.
4. <code>board</code> - a representation of the physical board you are using. This is created by specifying the Spark board and passing in the Particle Cloud API token and the device ID.

Now that the objects are created, you can get to the meat of the application. Johnny-Five provides a board 'ready' construct that makes a callback when the board is on, initialized and ready for action. Inside the ananymous callback function is where your application code executes (this function is invoked when the board is ready for use).

Johnny-Five provides a collection of objects that represent the board, the pins on the board, and various types of sensors and devices that could be connected to the board. For this lab you are going to write code that is fairly true to the base Arduino C/sketch or Particle Build programming model (we'll get into the abstractions that Johnny-Five provides you later). This will help you understand some of the basic concepts for how an Arduino or Particle board works.

In the following code you will create a callback function that is invoked when the Photon is initialized and ready (this is a Johnny-Five concept). You will set digital pin _D7_ (the <code>LEDPIN</code> variable above) as an output pin (vs. an input pin), meaning the application is expecting to send voltage out from the pin as opposed to read the voltage coming in to the pin. Then you will create a loop that runs once per second and inside that loop you will write out to the pin either LOW or HIGH voltage. Since pin _D7_ is a digital pin, its only options are 0 and 1 - in the world of Arduino-based boards that is LOW and HIGH. When you send 0 (or LOW) to the pin, that is equivalent to off (sending no voltage). When you send 1 (or HIGH) to the pin that is equivalent to on (sending full voltage).

{% highlight javascript %}
// The board.on() executes the anonymous function when the
// board reports back that it is initialized and ready. 
board.on("ready", function() { 
	console.log("Board connected..."); 
	// Set the pin you connected to the LED to OUTPUT mode  
	this.pinMode(LEDPIN, five.Pin.OUTPUT); 

	// Create a loop to "flash/blink/strobe" an led  
	var val = 0;  this.loop( 1000, function() {
		this.digitalWrite(LEDPIN, (val = val ? 0 : 1));
	});
});
{% endhighlight %}
  
Johnny-Five actually has an object model for an LED and we could also have simply done the following, but I wanted you to see how the <code>digitalWrite()</code> function works before abstracting it away.

{% highlight javascript %}
board.on("ready", function() {
	var led = new five.Led(LEDPIN);
	led.blink(1000);
});
{% endhighlight %}

# Run the App
When you run the application it will execute on your computer, and thanks to Johnny-Five, it will connect with your board and work directly with it. Basically, your computer is acting as a hub and communicating via Wi-Fi with the Photon as one of potentilly many devices (or spokes). In a future lab you will deploy the Node.js application to another device (like a Rasberry Pi 2) which will act as the hub and connect to multiple spoke devices.

Open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following commands (replace c:\Development\IoTLabs with the path that leads to your labs folder):

<pre>
  cd C:\Development\IoTLabs
  node blinky.js
</pre>

You should the indicator LED on the board blink a little as the app is initialized, and then the on-board LED next to pin _D7_ and the LED you connected should start blinking in unison at one blink per second.

<blockquote>
Note: The Photon doesn't need to be plugged into your PC - it simply needs to be powered on and on the same Wi-Fi you configured it for (and the PC running the Node.js app has to be on the same Wi-Fi network). 
</blockquote>
  
When you want to quite the application, press <kbd>CTRL</kbd> + <kbd>C</kbd> twice to exit the program without closing the window (you may also have to press <kbd>Enter</kbd>). 

<blockquote>
  PARTICLE PHOTON USERS: After stopping the application press the _Reset_ button on the Photon to prepare it for the next run.
</blockquote>

# Conclusion &amp; Next Steps
In this lab you learned how to write a Node.js/Johnny-Five application that writes LOW and HIGH signals to a digital pin (designated for output) to make an LED blink. In itself this may not be very exciting, but the core concept is necessary - writing to a digital output pin.

In the next lab you will learn how to read voltage coming in on an analog pin, and you will learn how to use a voltage divider to capture the variable resistance provided by a photoresistor.

<a class="radius button small" href="{{ site.url }}/device/photon/reading-analog-input/">Go to 'Reading Analog Input' ›</a>

[1]: https://store.particle.io/?product=photon-kit