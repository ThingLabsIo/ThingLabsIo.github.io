---
layout: page-photon
title: "Reading Analog Input"
subheadline: "Building Connected Things with the Particle Photon"
teaser: "In this lab you will read input from an analog sensor using a voltage divider."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [photon, iot, maker, javascript, node.js, johnny-five]
permalink: /device/photon/reading-analog-input/
---
If you haven't already done so, please follow the instructions in ['Getting Started'](../getting-started/) section.

### Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will use two resistors - a static resistor and a variable resistor - to create a voltage divider that enables you to effectively understand the intensity of light detected by a photoresistor - essentially a light meter. In the previous lesson you learned how to send OUTPUT and in this lesson you will learn to collect INPUT.

## Bill of Materials

What you will need:

1. [Particle Photon Development Kit][1]
2. USB to micro-USB cable (there is one included in the [Photon Development Kit][1])
3. Photoresistor (there is one included in the [Photon Development Kit][1])
4. 220-Ohm 1/4 Watt resistor (there is one included in the [Photon Development Kit][1])

For this lab series you are using a Particle Photon, a small Wi-Fi enabled development board. The Photon is an excellent prototyping board for connected Things and Particle, the makers of the Photon, sell the P0 chip that drives the board (so when you are ready to go into production you can easily use the same chip). While you can develop for the Photon using Particle Build or Particle Dev and leverage the Particle Cloud, this lab series is designed to teach you how to build a Wi-Fi based hub-and-spoke system, similar to how SmartThings or Phillips Hue works. For the first few labs you will learn how to create Node.js applications that run on your PC and control the Photon. Later in the lab you will learn how to deploy the Node.js applications to a hub device, like a Raspberry Pi 2 or an Arduino Y&uacute;n, which will act as the field gateway for all of the connected Photons in your solution.

## Wiring a Voltage Divider
The first step is to wire up the Photon to read voltage as determined by the resistance created by the photoresistor. Wire your board according to the diagram (wire colors don't matter, but help with identification of purpose).

The A0-A7 pins on the board enable you to read from or write to analog sensors, such as photoresistors, knobs (potentiometers), and temperature sensors. Here is the description of the analog pins from the [Particle Photon Datasheet](https://docs.particle.io/datasheets/photon-datasheet/#pin-and-button-definition):

<blockquote>
12-bit Analog-to-Digital (A/D) inputs (0-4095), and also digital GPIOs. A6 and A7 are code convenience mappings, which means pins are not actually labeled as such but you may use code like analogRead(A7). A6 maps to the DAC pin and A7 maps to the WKP pin.
</blockquote>

A <strong>photoresistor</strong>, also known as light-dependent resistor (LDR) or a photocell, works by limiting the amount of voltage that passes through it based on the intensity of light detected. The resistance decreases as light input increases - in other words, the more light, the more voltage passes through the photoresistor.

In order to take advantage of the photoresistor you will create a voltage divider - a passive linear circuit that splits the input voltage amongst two or more components (similar to a Y-splitter).

<img src="/images/photon_lab02_schem.png"/>

To create the voltage divider needed for this lesson you will:

- Connect the voltage from the 3.3-volt (input voltage) pin to a circuit (using a breadboard).
- Connect the input voltage to a static resistor (220-Ohm).
- Establish a voltage divider coming out of the static resistor:
   - One route to the analog pin (A0).
   - One route to a dynamic resistor (the photoresistor).
- Completing the circuit out of the dynamic resistor to ground.

As the photoresistor increases its resistance (lower light intensity) more of the input voltage coming out of the 220-Ohm resistor is blocked and diverted to the A0 pin. That means that the less intense the light into the photoresistor the more resistance it creates, which in turn diverts more voltage to the A0 pin (the voltage has to go somewhere). Likewise, the more intense the light into the photoresistor, the less resistance it creates, which in turn means there is less voltage to divert to the A0 pin.

In short, the more voltage to the A0 pin, the darker it is.

Here are the specific wiring instructions.

<img src="/images/photon_lab02_bb.png"/>

### Photoresistor
Insert a photoresistor into the breadboard as shown in the diagram.

### Resistor
Connect a 220-Ohm resistor from one side of the photoresistor across a couple of rows.

### Wires
Connect the wires as shown in the diagram:

#### Red
Connect the 3.3V pin to the red/positive side-rail on the breadboard.
Connect the red/positive side-rail to the row where the resistor lead is connected but the photoresistor is not (this is the input voltage into the static resistor part of the voltage divider).

#### Green
Connect the green wire from the other side of the static resistor (this should be in the same row as the static resistor lead and one of the photoresistor leads) to the A0 pin on the Photon (this is one route of the voltage divider - the other route is through the photoresistor).

#### Black
Connect the row holding the other lead from the photoresistor to the black/negative side-rail on the breadboard.
Connect the black/negative side-rail of the breadboard to one of the GND pins on the Photon.
This completes the circuit.

<blockquote>
Note: You could connect the 3.3V pin directly to the same row as the lone lead of the static resistor and the GND directly to the lead of the photoresistor, but I like building a habit of connecting the input voltage and ground pins from the board to the side rails. This will come in handy in the future lessons.
</blockquote>

## Writing the Code
For this lab you will create a new file named <strong>lab02.js</strong> in the same directory as you did in Lab 1. There are no additional dependencies, so you don't need to make any changes to the package.json file.

In the lab02.js file start by declaring the key objects, including a variable for the analog pin number you will use (A0 or 0). Most of this is identical to Lab 1.

{% highlight javascript %}
// Define the Johnny Five and Particle-IO variables
var five = require("johnny-five");
var particle = require("particle-io");

// Set up the access credentials for Particle and Azure 
var token = process.env.PARTICLE_KEY || 'YOUR PARTICLE ACCESS TOKEN HERE'; 
var deviceId = process.env.PHOTON_ID || 'YOUR PARTICLE PHOTON DEVICE ID/ALIAS HERE'; 

// Create a Johnny Five board instance to represent your Particle Photon.
// Board is simply an abstraction of the physical hardware, whether it is 
// a Photon, Arduino, Raspberry Pi or other board. 
var board = new five.Board({ 
	io: new Particle({ 
		token: token, 
		deviceId: deviceId 
	}) 
});

// Define the pin you will use to read the residual voltage 
// coming from the photoresistor
var ANALOGPIN = "A0";
{% endhighlight %}

Next, define the callback function in the Johnny-Five board initialization. For this lab you will use the <code>analogRead()</code> function, which takes the analog pin number in as input and calls a callback function when input is read off the pin. In the callback function, simply write the data to the console.

{% highlight javascript %}
// The board.on() executes the anonymous function when the 
// board reports back that it is initialized and ready.
board.on("ready", function() {
    console.log("Board connected...");
    // Read the residual voltage coming from the photoresistor
    this.analogRead(ANALOGPIN, function(val) {
        // The analogRed() function has a value range of 0-1023 (1024 steps).
        // The circuit is pulling 3.3V from the 3V3 pin.
        // Determine the residual voltage that is not going through the
        // photoresistor by multiplying the value on pin A0 by 3.3V / 1024.
        console.log(val * (3.3 / 1024.0));
    }); 
});
{% endhighlight %}

The value that the _analogRead()_ function is passing to the callback function (as the _val_ argument) is the amount of voltage that is not being pulled thhrough the photoresistor, and is instead being pulling into pin _A0_. The value is not the actual voltage, but rather a representation of the voltage as a range value between 0 and 1023. Since you are using a 3.3V power circuit you have to multiple the voltage value by 1024th of 3.3V (or 3.3 / 1024). The result is the actual voltage being pulled into analog pin A0.

If you want to compare your code to the final solution you can see the code in GitHub [here](https://github.com/ThingLabsIo/IoTLabs/blob/master/Photon/Lab02/lab02.js).

## Run the Application
To run the application, plug the Photon into a power supply using the USB cable. Open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following commands (replace _C:\Development\IoTLabs_ with the path that leads to your labs folder):

<pre>
  cd C:\Development\IoTLabs
  node lab02.js
</pre>

You should see the indicator LED blink a little as the app is initialized, and then you should see something like the following in the terminal/console window (the actual values will depend on how much light the photoresistor is receiving):

<pre>
c:\Development\IoTLabs>node lab02.js
1440573763366 Device(s) particle-io
1440573764086 Connected particle-io
1440573764109 Repl Initialized
>> 2.6876953125
2.6876953125
2.69091796875
2.69091796875
2.6876953125
2.8423828125
3.12275390625
3.15498046875
3.158203125
3.158203125
3.18076171875
3.2419921874999997
3.261328125
3.261328125
3.261328125
3.18720703125
3.1646484375
3.1646484375
3.1646484375
3.1646484375
</pre>

While the application is running and you are seeing data in the console, try covering the photoresistor (thus decreasing the light and increasing the resistance from the photoresistor and pushing more voltage to pin A0) or shining a light on the photoresistor (thus increasing the light and decreasing the resistance from the photoresistor and allowing more voltage through to ground (effectively stealing voltage away from the A0 pin).

Press <kbd>CTRL</kbd> + <kbd>C</kbd> twice then <kbd>Enter</kbd>to exit the program without closing the window. After stopping the application press the _Reset_ button on the Photon to prepare it for the next run.

## Conclusion &amp; Next Steps
Congratulations! You have made your first device that understands its environment, you learned about a voltage divider, and how to read data from an analog input sensor. In the [next lab][nextlab] you will put together the two labs so far to make an LED that responds to the ambient light (and you will learn about Pulse-Width Modulation).

<a class="radius button small" href="{{ site.url }}/device/photon/input-controls-output/">Next Lab: Input Controls Output ›</a>

[1]: https://store.particle.io/?product=photon-kit
[nextlab]: ../input-controls-output/