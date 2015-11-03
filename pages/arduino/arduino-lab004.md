---
layout: page-fullwidth
title: "Input Controls Output"
subheadline: "Arduino + Azure IoT Lab 4"
teaser: "In this lab you will use analog input to control digital output."
show_meta: true
comments: true
header: no
breadcrumb: true
categories:
    - iot-arduino-labs
    - maker-101
permalink: "/arduino/04/"
---
If you haven't already done so, please follow the instructions in [Lab 00: Getting Started](../00/) section.

### Table of Contents
*  Auto generated table of contents
{:toc}

## Bill of Materials
What you will need (all the parts from the previous lessons):

1. [Arduino Uno][uno] or [Arduino Y&uacute;n][yun] 
2. USB to micro-USB cable
3. [5mm Green LED](http://www.sparkfun.com/products/12062)
4. [330-Ohm 1/4 Watt resistor](http://www.sparkfun.com/products/10969) (Orange-Orange-Brown)
5. [Photoresistor (5528)](http://www.sparkfun.com/products/9088)
6. [10k-Ohm 1/4 Watt resistor](http://www.sparkfun.com/products/10969) (Brown-Black-Orange)

## Wiring the Board
This lesson combines the previous two lessons, so it's likely that you already have most of the wiring done from the previous lesson. Wire the Arduino according to the diagram.

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
6. Connect digital pin 11 (a PWM pin) to the positive lead of the LED (the longer lead is the positive lead).
7. Connect the other lead from the LED to the 330 Ohm resistor.
8. Connect the other end of the 330 Ohm resistor to the negative side rail.

## Using Pulse Width Modulation (PWM)
Pulse Width Modulation (PWM) is a technique for simulating analog values on a digital pin. There are several digital pins on Arduino boards that support PWM depending on the board you are using. For example, the Arduino you are using in this lab supports 8-bit (0-255) PWM on digital pins 3, 5, 6, 9, 10, 11, and 13 using the <code>analogWrite()</code> function.

PWM simulates analog data by creating a square wave (basically a repeating switch between on and off) where the duration of 'on' time is the pulse width. If the square wave has a 50% pulse width (more commonly known as a duty cycle), then the output from that pin is equal amounts on and off. If the duty cycle is 25% then the output from the pin will be on for only one-quarter of the duty cycle (inversely it will be off for three times as long as it is on - 25% on, 75% off).

<img src="/images/DutyCycle.jpg"/>

Because the time windows of a cycle is too fast for the human eye to perceive (about 2 milliseconds on the Arduino pins that support PWM), instead of causing an LED to strobe or flicker, it simply appears to be more or less bright. Using a 25% duty cycle the LED would be on (HIGH output) for half a millisecond and off (LOW output) for 1.5 milliseconds which makes the LED appear to be at 25% brightness. So while we aren't truly sending analog data to a digital LED, we are using PWM to simulate the effect of analog data.

## Writing the Code
For this lab you will create a new file named <strong>lab03.js</strong> in the same directory as you did in the previous labs. There are no additional dependencies, so we don't need to make any changes to the _package.json_ file.

In the lab03.js file start by declaring the key objects, including a variable for the LED pin and the analog pin you will use (digital pin 11 for the LED and analog pin A0 for the photoresistor - if you still have your project board wired up from the previous labs then you should be all set). You should also stub out the <code>board.on()</code> callback function for Johnny Five.

{% highlight javascript %}
'use strict';
// Define the Johnny Five component
var five = require("johnny-five");

// Create a Johnny Five board instance to represent your Arduino.
// Board is simply an abstraction of the physical hardware, whether it is 
// a Arduino, Raspberry Pi, Particle Photon, or other boards. 
var board = new five.Board();

// Define the pin that is connected to the LED 
var LEDPIN = 11;

// Define the pin you will use to read the residual voltage 
// coming from the photoresistor
var ANALOGPIN = 0;

// The board.on() executes the anonymous function when the
// board reports back that it is initialized and ready.
board.on("ready", function() {
  console.log("Board connected...");
 
});
{% endhighlight %}

Inside of the <code>board.on()</code> function you will first initialize the digital pin that you will use for the LED as a pulse width modulation (PWM) pin. Replace the <code>board.on()</code> code block with the following code.

{% highlight javascript %}
board.on("ready", function() {
  // Set pin 11 to PWM mode
  this.pinMode(LEDPIN, five.Pin.PWM);
 
  // TODO 03 - You will add the photoresistor code here.
  
});
{% endhighlight %}

Next you will use the <code>analogRead()</code> function to capture the data coming from the photoresistor. In [Lab 02](../02/) you simply wrote the data out to the console log. For this lab you will use the data to determine how bright the LED should be. The concept is the same, but the callback function you write this time will have a little more to it (but not much).

First, define the <code>analogRead()</code> function and the callback function that will be invoked when data input is received. The format is <code>this.analogRead( pinNumber, callbackFunction );</code> - add the following code where the <code>// TODO 03 - You will add the photoresistor code here.</code> comment is.

{% highlight javascript %}
  // Create a new 'photoresistor' hardware instance.
  var photoresistor = new five.Sensor({
    pin: ANALOGPIN  // Analog pin 0
  });
  
  // Define the callback function for the photoresistor reading.
  // The Sensor class raises the "data" event every 25ms by default.
  photoresistor.scale(0, 255).on("data", function() {
    var darkIntensity = this.value;

    // TODO: Write a value to the LED output pin

  });
{% endhighlight %}

The anonymous callback function is invoked at the frequency that the Sensor class is configured to read data from the photoresistor (every 25ms by default). The scope of the callback function is the Sensor object (the photoresistor in this case), meaning <code>this.value</code> is the value of voltage being read on pin _A0_. 

Your goal is to create an application that increases the LED brightness as the ambient room light decreases. The __scale(0, 255)__ function call will result in the value being read from pin _A0_ (Johnny-Five normalizes analog inputs to a range of 0-1023) to be scaled down to a value range of 0-255. The LED has a value range of 0-255, which means that the scaling matches the value range for the PWM output.

The final step is to set the value of the _OUTPUT_ pin to a brightness value. In [Lab 02](../02/) you used <code>digitalWrite()</code> to set the OUTPUT pin to either HIGH or LOW (1 or 0). Since you defined the LED OUTPUT pin as a PWM pin you will use <code>analogWrite()</code> instead, which will tell the board to simulate an analog device using PWM. For debugging purposes you can also add a <code>console.log()</code> call. Add the following code where the <code>TODO: Write a value to the LED output pin</code> comment is.

{% highlight javascript %}
    // Write the value to the PWM output pin
    // As the detected light intensity decreases (it gets darker)
    // the value coming in on pin A0 increases.
    // Using the value as output will make the LED grow brighter
    // as the room gets darker.
    board.analogWrite(LEDPIN, darkIntensity);

    console.log('value: ' + darkIntensity + ' ; voltage: '  + (darkIntensity * (5.0 / 256.0)));

{% endhighlight %}

That's all the code - now you are ready to run the application.

If you want to compare your code to the final solution you can see the code in GitHub [here](https://github.com/ThingLabsIo/IoTLabs/blob/master/Arduino/Labs01_04/lab04.js).

## Run the Application
To run the application, plug your Arduino into your computer with the USB cable. Open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following commands (replace _C:\Development\IoTLabs_ with the path that leads to your labs folder):

<pre>
cd C:\Development\IoTLabs
node lab03.js
</pre>

You should see some lights on the board blink a little as the app is initialized, and then you should see something like the following in the terminal/console window (the actual values will depend on how much light the photoresistor is receiving):
<pre>
C:\Development\IoTLabs>node lab03.js
1429678663007 Device(s) COM3
1429678663038 Connected COM3
1429678668085 Repl Initialized
>> val: 1.5478515625; brightness: 0
val: 1.552734375; brightness: 0
val: 1.5478515625; brightness: 0
val: 1.5478515625; brightness: 0
val: 1.5478515625; brightness: 0
val: 1.5478515625; brightness: 0
val: 1.5478515625; brightness: 0
val: 1.5478515625; brightness: 0
val: 1.552734375; brightness: 0
val: 1.5478515625; brightness: 0
val: 1.5478515625; brightness: 0
val: 1.5478515625; brightness: 0
val: 1.5478515625; brightness: 0
val: 1.5478515625; brightness: 0
val: 1.5478515625; brightness: 0
val: 1.5478515625; brightness: 0
</pre>
With the application running try changing the amount of light the photoresistor is receiving by slowly covering it with your hand. As the amount of light it detects reduces, the resistance will decrease and more voltage will pass into the INPUT pin. As that happens, thanks to your code, the voltage to the LED over the OUTPUT pin will increase and the LED will glow brighter. You should also notice that there is a minimum amount of light that will cause the LED to turn off all together. That is because of the mapping of the input value of 350 to the output value of 0 (if the room you are in is too dim and the LED is never shutting off, you can increase 350 to something like 500 and see what happens).

Press <kbd>CTRL</kbd> + <kbd>C</kbd> twice then <kbd>Enter</kbd>to exit the program without closing the window.

## Conclusion &amp; Next Steps
Congratulations! You made a device that both detects its environment and responds to it. You learned about Pulse Width Modulation and the analogWrite() function for simulating analog behavior on a digital device, and you learned how to recreate the Arduino map() and constrain() functions.

In the [next lab][nextlab] you will learn how to start sending data from your Arduino to the Cloud.

[Next Lab ->][1]

{% include next-previous-post-in-category.html %}

[uno]: http://www.arduino.cc/en/Main/ArduinoBoardUno
[yun]: http://www.arduino.cc/en/Main/ArduinoBoardYun
[nextlab]: /arduino/05/