---
layout: page-photon
title: "Input Controls Output"
subheadline: "Building Connected Things with the Particle Photon"
teaser: "In this lab you will read input from an analog sensor using a voltage divider and control an LED based on the input reading."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [photon, iot, maker, javascript, node.js, johnny-five]
permalink: "/device/photon/input-controls-output/"
---
If you haven't already done so, please follow the instructions in ['Getting Started'](../getting-started/) section.

### Table of Contents
*  Auto generated table of contents
{:toc}

## Bill of Materials
What you will need (all the parts from the previous lessons):

1. [Particle Photon Development Kit][1]
2. USB to micro-USB cable (there is one included in the [Photon Development Kit][1])
3. LED (there is one included in the [Photon Development Kit][1])
4. Photoresistor (there is one included in the [Photon Development Kit][1])
5. Two (2) 220-Ohm 1/4 Watt resistor (there are two included in the [Photon Development Kit][1])
6. [One M/M jumper wire](https://www.sparkfun.com/products/11026)

For this lab series you are using a Particle Photon, a small Wi-Fi enabled development board. The Photon is an excellent prototyping board for connected Things and Particle, the makers of the Photon, sell the P0 chip that drives the board (so when you are ready to go into production you can easily use the same chip). While you can develop for the Photon using Particle Build or Particle Dev and leverage the Particle Cloud, this lab series is designed to teach you how to build a Wi-Fi based hub-and-spoke system, similar to how SmartThings or Phillips Hue works. For the first few labs you will learn how to create Node.js applications that run on your PC and control the Photon. Later in the lab you will learn how to deploy the Node.js applications to a hub device, like a Raspberry Pi 2 or an Arduino Y&uacute;n, which will act as the field gateway for all of the connected Photons in your solution.

## Wiring the Board
In this lab you will creat two distinct circuits using the Photon. This lesson combines the previous two lessons, so it's likely that you already have most of the wiring done from the previous lesson. Wire the Photon according to the diagram.

### Circuit 1

1. Connect one side of the photoresistor to pin _A0_ and the other side to _GND_.
2. Connect one of the 220 Ohm resistors to pin _A0_ (this creates the voltage divider).
3. Connect the other end of the resistor to an open row on the breadboard.
4. Using a jumper wire, connect the _3V3_ pin to the same row as the resistor.

### Circuit 2

1. Connect digital pin _D0_ to the positive lead of the LED (the longer lead is the positive lead).
3. Connect the other lead from the LED to the negative siderail.
4. Connect the other 220 Ohm resistor from the negative siderail to the _GND_ pin (next to the _D7_ pin).

<img src="/images/photon_lab03_bb.png"/>

## Using Pulse Width Modulation (PWM)
Pulse Width Modulation (PWM) is a technique for simulating analog values on a digital pin. There are several pins on the Photon that support PWM. Here is what the Photon datasheet says:

<blockquote>
  PWM is available on D0, D1, D2, D3, A4, A5, WKP, RX, TX with a caveat: PWM timer peripheral is duplicated on two pins (A5/D2) and (A4/D3) for 7 total independent PWM outputs. For example: PWM may be used on A5 while D2 is used as a GPIO, or D2 as a PWM while A5 is used as an analog input. However A5 and D2 cannot be used as independently controlled PWM outputs at the same time.
</blockquote>

PWM simulates analog data by creating a square wave (basically a repeating switch between on and off) where the duration of 'on' time is the pulse width. If the square wave has a 50% pulse width (more commonly known as a duty cycle), then the output from that pin is equal amounts on and off. If the pulse width (or duty cycle) is 25% then the output from the pin will be on for only one-quarter of the duty cycle (inversely it will be off for three times as long as it is on - 25% on, 75% off).

<img src="/images/DutyCycle.jpg"/>

Because the time windows of a cycle is too fast for the human eye to perceive (about 2 milliseconds on the Photon pins that support PWM), instead of causing an LED to strobe or flicker, it simply appears to be more or less bright. Using a 25% duty cycle the LED would be on (HIGH output) for half a millisecond and off (LOW output) for 1.5 milliseconds which makes the LED appear to be at 25% brightness. So while we aren't truly sending analog data to a digital LED, we are using PWM to simulate the effect of analog data.

## Writing the Code
For this lab you will create a new file named <strong>inandout.js</strong> in the same directory as you did in the previous labs. There are no additional dependencies, so you don't need to make any changes to the package.json file.

In the _inandout.js_ file start by declaring the key objects, including a variable for the LED pin and the analog pin you will use (digital pin _D0_ for the LED and analog pin _A0_ for the photoresistor - if you still have your project board wired up from the previous labs then you should be all set). You should also stub out the <code>board.on()</code> callback function for Johnny Five.

{% highlight javascript %}
'use strict'
// Define the Jonny Five and Particle-IO variables
var five = require("johnny-five");
var particle = require("particle-io");

// Set up the access credentials for Particle and Azure 
var token = process.env.PARTICLE_KEY || 'YOUR PARTICLE ACCESS TOKEN HERE'; 
var deviceId = process.env.PHOTON_ID || 'YOUR PARTICLE PHOTON DEVICE ID/ALIAS HERE'; 

// Define the pin that is connected to the LED
var LEDPIN = "D0";
// Define the pin you will use to read the residual voltage 
// coming from the photoresistor
var ANALOGPIN = "A0";

// Create a Johnny-Five board instance to represent your Particle Photon
// Board is simply an abstraction of the physical hardware, whether is is a 
// Photon, Arduino, Raspberry Pi or other boards.
var board = new five.Board({
  io: new Particle({
    token: token,
    deviceId: deviceId
  })
});
// The board.on() executes the anonymous function when the 
// board reports back that it is initialized and ready.
board.on("ready", function() {
  console.log("Board connected...");
  
});
{% endhighlight %}

Inside of the <code>board.on()</code> function you will first initialize the digital pin that you will use for the LED as a pulse width modulation (PWM) pin. Replace the <code>board.on()</code> code block with the following code.

{% highlight javascript %}
board.on("ready", function() {
  console.log("Board connected...");
  
  // Set the pin you connected to the LED to OUTPUT mode
  this.pinMode(LEDPIN, five.Pin.PWM);
 
  // TODO: You will add the photoresistor code here.
  
});
{% endhighlight %}

Next you will use the __Sensor__ class to represent the photoresistor and capture the light/darkness measurement. In [Lab 02](/photon/02/) you used _analogRead()_ to collect and log the voltage value out to the console. For this lab you will use the light/darkness value to determine how bright the LED should be. Start by defining a _photoresistor_ variable using the __Sensor__ class. Next create a hander function for the photoresistor _data_ event. Add the following code where the <code>// TODO: You will add the photoresistor code here.</code> comment is.

{% highlight javascript %}
	  // Create a new 'photoresistor' hardware instance.
	  var photoresistor = new five.Sensor({
		    pin: ANALOGPIN  // Analog pin 0
	  });
    
	  // Define the callback function for the photoresistor reading.
	  // The Sensor class raises the "data" event every 25ms by default.
	  photoresistor.scale(0, 255).on("data", function() {
		    // Write the value to the PWM output pin
		    // As the detected light intensity decreases (it gets darker)
		    // the value coming in on pin A0 increase.
		    // Using the value as output will make the LED grow brighter
		    // as the room gets darker.
		    board.analogWrite(LEDPIN, this.value);
        
        // For debugging purposes, log the values out to the console.
        console.log(this.value);
	  });
{% endhighlight %}

The __scale(0, 255)__ function call will result in the value being read from pin _A0_ - which has a value range of 0-1023 - to be scaled down to a value range of 0-255. The LED has a value range of 0-255, which means that the scaling matches the value range for the PWM output.

If you want to compare your code to the final solution you can see the code in GitHub [here](https://github.com/ThingLabsIo/IoTLabs/blob/master/Photon/Lab03/lab03.js).

## Run the Application
To run the application, plug the Photon into a power supply using the USB cable. Open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following commands (replace _C:\Development\IoTLabs_ with the path that leads to your labs folder):

<pre>
cd C:\Development\IoTLabs
node inandout.js
</pre>

You should see the indicator LED blink a little as the app is initialized, and then you should see something like the following in the terminal/console window (the actual values will depend on how much light the photoresistor is receiving):
<pre>
c:\Development\IoTLabs>node inandout.js
1446321771050 Device(s) particle-io  
1446321772194 Connected particle-io  
1446321772266 Repl Initialized  
>> Board connected...
null
null
248.02052785923755
248.02052785923755
248.02052785923755
247.77126099706746
247.77126099706746
</pre>

With the application running try changing the amount of light the photoresistor is receiving by slowly covering it with your hand. As the amount of light it detects reduces, the resistance will increase and more residual voltage will pass into the _A0_ pin. As that happens, thanks to your code, the voltage to the LED over the _D0_ pin will increase and the LED will glow brighter.

Press <kbd>CTRL</kbd> + <kbd>C</kbd> twice then <kbd>Enter</kbd>to exit the program without closing the window. After stopping the application press the _Reset_ button on the Photon to prepare it for the next run.

## Conclusion &amp; Next Steps
Congratulations! You made a device that both detects its environment and responds to it. You learned about Pulse Width Modulation and the analogWrite() function for simulating analog behavior on a digital device, and you learned how to recreate the Arduino map() and constrain() functions.

In the [next lab][2] you will learn how to start sending data from your Photon to the Cloud.

<a class="radius button small" href="{{ site.url }}/lang/js/azure/">Next Lab: Input Controls Output ›</a>

[1]: https://store.particle.io/?product=photon-kit
[2]: /lang/js/azure/