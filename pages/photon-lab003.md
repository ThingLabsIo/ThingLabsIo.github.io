---
layout: page-fullwidth
title: "Input Controls Output"
subheadline: "Particle Photon IoT Lab 3"
teaser: "In this lab you will use analog input to control digital output."
show_meta: true
comments: true
header: no
breadcrumb: true
categories:
    - iot-photon-labs
    - maker-101
permalink: "/photon/03/"
---
If you haven't already done so, please follow the instructions in [Lab 00: Getting Started](/photon/00/) section.

### Table of Contents
*  Auto generated table of contents
{:toc}

## Bill of Materials
What you will need (all the parts from the previous lessons):

1. [Particle Photon][1]
2. USB to micro-USB cable (there is one included in the [Photon Development Kit][1])
3. LED (there is one included in the [Photon Development Kit][1])
4. Two (2) 220-Ohm 1/4 Watt resistor (there are two included in the [Photon Development Kit][1])
5. Photoresistor (there is one included in the [Photon Development Kit][1])
7. [Jumper wires](https://www.sparkfun.com/products/12795)

For this lab series you are using a Particle Photon, a small Wi-Fi enabled development board. The Photon is an excellent prototyping board for connected Things and Particle, the makers of the Photon, sell the P0 chip that drives the board (so when you are ready to go into production you can easily use the same chip). While you can develop for the Photon using Particle Build or Particle Dev and leverage the Particle Cloud, this lab series is designed to teach you how to build a Wi-Fi based hub-and-spoke system, similar to how SmartThings or Phillips Hue works. For the first few labs you will learn how to create Node.js applications that run on your PC and control the Photon. Later in the lab you will learn how to deploy the Node.js applications to a hub device, like a Raspberry Pi 2 or an Arduino Y&uacute;n, which will act as the field gateway for all of the connected Photons in your solution.

## Wiring the Board
This lesson combines the previous two lessons, so it's likely that you already have most of the wiring done from the previous lesson. Wire the Photon according to the diagram.

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
6. Connect digital pin _D0_ to the positive lead of the LED (the longer lead is the positive lead).
7. Connect the other lead from the LED to the negative siderail (opposite the wiring in steps 1-6).
8. Connect the other 220 Ohm resistor from the negative siderail to the _GND_ pin next to the _D7_ pin.

## Using Pulse Width Modulation (PWM)
Pulse Width Modulation (PWM) is a technique for simulating analog values on a digital pin. There are several pins on the Photon that support PWM. Here is what the Photon datasheet says:

<blockquote>
  PWM is available on D0, D1, D2, D3, A4, A5, WKP, RX, TX with a caveat: PWM timer peripheral is duplicated on two pins (A5/D2) and (A4/D3) for 7 total independent PWM outputs. For example: PWM may be used on A5 while D2 is used as a GPIO, or D2 as a PWM while A5 is used as an analog input. However A5 and D2 cannot be used as independently controlled PWM outputs at the same time.
</blockquote>

PWM simulates analog data by creating a square wave (basically a repeating switch between on and off) where the duration of 'on' time is the pulse width. If the square wave has a 50% pulse width (more commonly known as a duty cycle), then the output from that pin is equal amounts on and off. If the pulse width (or duty cycle) is 25% then the output from the pin will be on for only one-quarter of the duty cycle (inversely it will be off for three times as long as it is on - 25% on, 75% off).

<img src="/images/DutyCycle.jpg"/>

Because the time windows of a cycle is too fast for the human eye to perceive (about 2 milliseconds on the Photon pins that support PWM), instead of causing an LED to strobe or flicker, it simply appears to be more or less bright. Using a 25% duty cycle the LED would be on (HIGH output) for half a millisecond and off (LOW output) for 1.5 milliseconds which makes the LED appear to be at 25% brightness. So while we aren't truly sending analog data to a digital LED, we are using PWM to simulate the effect of analog data.

## Writing the Code
For this lab you will create a new file named <strong>lab03.js</strong> in the same directory as you did in the previous labs. There are no additional dependencies, so you don't need to make any changes to the package.json file.

In the lab03.js file start by declaring the key objects, including a variable for the LED pin and the analog pin you will use (digital pin _D0_ for the LED and analog pin _A0_ for the photoresistor - if you still have your project board wired up from the previous labs then you should be all set). You should also stub out the <code>board.on()</code> callback function for Johnny Five.

{% highlight javascript %}
// Define the Jonny Five and Spark-IO variables
var five = require("johnny-five");
var Spark = require("spark-io");
// Define the Johnny Five board as your Particle Photon
var board = new five.Board({
  io: new Spark({
    token: process.env.PARTICLE_KEY || 'YOUR API KEY HERE',
    deviceId: process.env.PARTICLE_DEVICE || 'YOUR DEVICE ID OR ALIAS HERE'
  })
});

// Define the pin that is connected to the LED
var LEDPIN = "D0";
// Define the pin you will use to read the residual voltage 
// coming from the photoresistor
var ANALOGPIN = "A0";
// The board.on() executes the anonymous function when the 
// Partile Photon reports back that it is initialized and ready.
board.on("ready", function() {
  // TODO 01 - Add the code to read data from the analog pin
});

// TODO 02 - You will add a couple support functions here later in the lab
{% endhighlight %}

Inside of the <code>board.on()</code> function you will first initialize the digital pin that you will use for the LED as a pulse width modulation (PWM) pin. Replace the <code>board.on()</code> code block with the following code.

{% highlight javascript %}
board.on("ready", function() {
  // Set the pin you connected to the LED to OUTPUT mode
  this.pinMode(LEDPIN, five.Pin.PWM);
 
  // TODO 03 - You will add the analogRead code here.
  
});
{% endhighlight %}

Next you will use the <code>analogRead()</code> function to capture the data coming from the photoresistor. In [Lab 02](/photon/02/) you simply wrote the data out to the console log. For this lab you will use the data to determine how bright the LED should be. The concept is the same, but the callback function you write this time will have a little more to it (but not much).

First, define the <code>analogRead()</code> function and the callback function that will be invoked when data input is received. The format is <code>this.analogRead( pinNumber, callbackFunction );</code> - add the following code where the <code>// TODO 03</code> comment is.

{% highlight javascript %}
   // Read the input on analog pin 0:
  this.analogRead(ANALOGPIN, function(val) {
  
    // TODO 04 - map the value read to the correct range for the LED
  
  });
{% endhighlight %}

This function tells the application to read the data from the analog pin (_A0_ in this lab) and when input is collected, invoke the callback function in the second argument and pass the input data in the val argument.

Your goal is to create an application that increases the LED brightness as the ambient room light decreases. If it is light in the room, you don't want the LED to illuminate, and the darker it gets the brighter you want the LED to get. You also want some threshold of ambient light where the LED turns off (i.e. you don't want to have the LED faintly illuminated in a moderately bright room - it just wears the LED out). The way to do this is to map the incoming voltage from the photoresistor to the output voltage for the LED. There is a mismatch though. The photoresistor input is in a 10-bit range of 0-1023 and the PWM supported by our board is an 8-bit range of 0-255.

If you were doing this in C using the Arduino structures, values and functions without using frameworks such as Johnny-Five, you could use the <code>map()</code> function to map the input value into a new data range, such as mapping the analog input value (ranged to 0-1023) to a new range of 0-255. You can do that here - you just have to write the <code>map()</code> function yourself. Add the following code to the bottom of the lab03.js file (_after_ the end of the <code>board.on()</code> function, where the <code>// TODO 02</code> comment is.

{% highlight javascript %}
// This function maps a value from one range into another range
// Example: map (25, 0, 25, 0, 50) returns 50
// Example: map (20, 0, 100, 0, 10) returns 2
function map(x, in_min, in_max, out_min, out_max) {
  return Math.round((x - in_min) * (out_max - out_min) / (in_max - in_min) + out_min);
}

// TODO 05 another support method here.
{% endhighlight %}

This is the [exact formula](http://www.arduino.cc/en/Reference/Map) used by the Arduino. Using this function you can pass in a value and its predefined range, and the desired data range and it will return the appropriate value for that range.

The <code>map()</code> function will return negative values so you need to also recreate the [Arduino Constrain function](http://www.arduino.cc/en/Reference/Constrain), which will enable you to ensure the value is constrained to a minimum and maximum value. Add the following code immediately after the <code>map()</code> function, where the <code>// TODO 05</code> comment is.

{% highlight javascript %}
// This function ensures a value is within a defined range
function constrain(x, in_min, in_max) {
  return Math.round(x < in_min ? in_min : x > in_max ? in_max : x);
}
{% endhighlight %}

Now that you have written the supporting functions for <code>map()</code> and <code>constrain()</code> you can write the callback function for the <code>analogRead()</code> function you started writing inside the <code>board.on()</code> function. First you need to use the <code>map()</code> function to map the incoming value from the photoresistor to a brightness value for the LED. Add the following code where the <code>// TODO 04</code> comment is.

{% highlight javascript %}
    // Map the analog value (0-1023) to an 8-bit value (0-255)
    // so it can be used to define the LED output.
    var brightness = map(val, 950, 1023, 0, 255);
{% endhighlight %}

When you set the brightness value you mapped the incoming data from a range of 950-1000 to 0-255. In doing so you effectively set the ambient light value of 950 to a brightness of 0 (since 950 maps to 0). If the value coming in from the photoresistor is below 600 then the <code>map()</code> function will return a negative value. To account for the potential for a negative value you can constrain the value to the 0-255 range using the <code>constrain()</code> function you wrote. Add the following code immediately after the last code you added inside the <code>this.analogRead(...)</code> method.

{% highlight javascript %}
    // Use the constrain function to ensure the right values
    brightness = constrain(brightness, 0, 255);
{% endhighlight %}

This will ensure that the value of brightness is between 0 and 255.

The final step is to set the value of the OUTPUT pin to the value of brightness. In [Lab 01](/photon/01/) you used <code>digitalWrite()</code> to set the OUTPUT pin to either HIGH or LOW (1 or 0). Since you defined the LED OUTPUT pin as a PWM pin you will use <code>analogWrite()</code> instead, which will tell the board to simulate an analog device using PWM. For debugging purposes you can also add a <code>console.log()</code> call. Add the following code immediately after the last code you added inside the <code>this.analogRead(...)</code> method.

{% highlight javascript %}
    console.log('val: ' + (val * (3.3 / 1024.0)) + '; brightness: ' + brightness);

    // Set the brigthness of the LED
    this.analogWrite(LEDPIN, brightness);
{% endhighlight %}

That's all the code - now you are ready to run the application.

If you want to compare your code to the final solution you can see the code in GitHub [here](https://github.com/ThingLabsIo/IoTLabs/blob/master/Photon/Lab03/lab03.js).

## Run the Application
To run the application, plug the Photon into a power supply using the USB cable. Open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following commands (replace _C:\Development\IoTLabs_ with the path that leads to your labs folder):

<pre>
cd C:\Development\IoTLabs
node lab03.js
</pre>

You should see the indicator LED blink a little as the app is initialized, and then you should see something like the following in the terminal/console window (the actual values will depend on how much light the photoresistor is receiving):
<pre>
c:\Development\gh\IoTLabs\Photon\Lab03>node lab03.js
1440745763751 Device(s) spark-io
1440745764610 Connected spark-io
1440745764735 Repl Initialized
>> val: 899; voltage: 2.89716796875; brightness: 0
val: 899; voltage: 2.89716796875; brightness: 0
val: 899; voltage: 2.89716796875; brightness: 0
val: 899; voltage: 2.89716796875; brightness: 0
val: 899; voltage: 2.89716796875; brightness: 0
val: 900; voltage: 2.900390625; brightness: 0
val: 900; voltage: 2.900390625; brightness: 0
val: 900; voltage: 2.900390625; brightness: 0
val: 900; voltage: 2.900390625; brightness: 0
val: 900; voltage: 2.900390625; brightness: 0
val: 900; voltage: 2.900390625; brightness: 0
</pre>

With the application running try changing the amount of light the photoresistor is receiving by slowly covering it with your hand. As the amount of light it detects reduces, the resistance will decrease and more voltage will pass into the INPUT pin. As that happens, thanks to your code, the voltage to the LED over the OUTPUT pin will increase and the LED will glow brighter. You should also notice that there is a minimum amount of light that will cause the LED to turn off all together. That is because of the mapping of the input value of 950 to the output value of 0 (if the room you are in is too dim and the LED is never shutting off, you can increase 950 to something like 1000 and see what happens).

Press <kbd>CTRL</kbd> + <kbd>C</kbd> twice then <kbd>Enter</kbd>to exit the program without closing the window. After stopping the application press the _Reset_ button on the Photon to prepare it for the next run.

## Conclusion &amp; Next Steps
Congratulations! You made a device that both detects its environment and responds to it. You learned about Pulse Width Modulation and the analogWrite() function for simulating analog behavior on a digital device, and you learned how to recreate the Arduino map() and constrain() functions.

In the [next lab][2] you will learn how to start sending data from your Photon to the Cloud.

[Next Lab ->][2]

{% include next-previous-post-in-category.html %}

[1]: https://store.particle.io/?product=photon-kit
[2]: /photon/04/