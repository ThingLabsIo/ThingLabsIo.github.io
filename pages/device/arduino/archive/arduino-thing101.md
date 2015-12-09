---
layout: page-fullwidth
title: "Setting Up Your Arduino Firmware"
subheadline: "Arduino Thing 101 Labs"
teaser: "In this lab you will create a Thing that write output, reads input and ultimately the input will control the output."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [arduino, iot, maker, javascript, node.js, johnny-five]
author: doug_seven
permalink: "/device/arduino/thing-101/"
---

### Table of Contents
*  Auto generated table of contents
{:toc}

If you haven't already done so, please follow the instructions in ['Getting Started'](../getting-started/) section.

# Setting Up Your Arduino
In this lab you will install the Standard Firmatta onto your Arduino. The Standard Firmatta exposes basic input/output on the device for your gateway application to access.

## Bill of Materials
What you will need:

1. [SparkFun RedBoard Programmed with Arduino - $19.95](https://www.sparkfun.com/products/12757), [Arduino Uno R3 - $24.95](https://www.sparkfun.com/products/11021), or [Arduino Y&uacute;n - $74.95](https://www.sparkfun.com/products/12053)
2. USB cable to connect the Arduino to your computer (the type will vary depending on the Arduino board you are using)
3. [5mm Green LED](http://www.sparkfun.com/products/12062)
4. [330-Ohm 1/4 Watt resistor](http://www.sparkfun.com/products/10969) (Orange-Orange-Brown)
5. [Photoresistor (5528)](http://www.sparkfun.com/products/9088)
6. [10k-Ohm 1/4 Watt resistor](http://www.sparkfun.com/products/10969) (Brown-Black-Orange)

## Install the Arduino IDE
While you won't use the Arduino IDE very much in the set of labs, it is necessary for a couple of things. For one thing, installing the Arduino IDE also installs the USB drivers for the Arduino board.

Go to [http://www.arduino.cc][arduino] and follow the links to download the latest version of the Arduino IDE. Make sure that the checkbox for the USB driver is selected during install (it typically is by default).

## Upload the Standard Firmata to your Arduino
For this lab series you will use tools that require your Arduino to have the Standard Firmatta loaded. Fortunately, this is easy to do using the Arduino IDE.

1. Connect your Arduino board via USB to your computer.
2. Open the Arduino IDE.
3. Using the _Tools_ menu, select _Board_ and make sure the type of board you are using (Arduino Uno or Arduino Y&uacute;n) is selected.
4. Using the _Tools_ menu, select _Port_ and make sure your board is selected on a COM port.
5. Using the _File_ menu, select _Examples_ > _Firmata_ > _StandardFirmata_. This will open a new Arduino IDE window with the code for the Standard Firmata loaded.
6. Using the _Sketch_ menu, select _Upload_. This will upload the firmware to your Arduino (you should see the lights on the Arduino blink while this happens).
7. Close all Arduino IDE windows.

# Writing DIgital Output
In this lab you will use one of the digital output pins to send a signal to an LED. You will use digital pin 13, which also has an LED on the board connected to it (so you will be able to see it turn on and off even if you mis-wire something). While this lesson could be done using only the onboard LED, one of the objectives of this lab is to familiarize you with connecting input and output devices (an LED is an output device) to the board and controlling them with software.

## Wiring the LED
Next you need to wire up the Arduino board so that it can send on and off commands to the LED. You can wire your board according to the diagram (wire colors don't matter, but help with identification of purpose).
<img src="/images/lab01_bb.png"/>

### LED
Insert a LED into the breadboard as shown in the diagram. For reference, an LED has one lead that is longer than the other. The longer lead is the positive (+) lead, and the shorter lead is the negative (-) lead.

### Wires
Connect the wires as shown in the diagram:

#### Black
Connect the GND pin to the negative (-) side-rail on the breadboard.

#### Yellow
Connect pin 13 to the breadboard in the same row as the positive (longer) lead from the LED.

### Resistor
Connect a 330-Ohm resistor from the row that the negative (shorter) lead from the LED is in to the negative (-) side-rail.

## Write the Code
Since we are using Node.js and Johnny-Five for this lab we can take advantage of the dependency management capabilities that Node.js provides. We need to let our application know that it has a dependency on the Johnny-Five framework so that when the application is prepared for execution, it can fetch the required dependencies for us. In Node.js this is done with a _package.json_ file. This file provides some basic meta-data about the application, including any dependencies on packages that can be retrieved using the Node Package Manager (NPM).

Using your favorite/preferred text/code editor, create a file in your labs folder named __package.json__ and add the following:

{% highlight json %}
{
  "name": "IoT-Labs-Arduino",
  "repository": {
    "type": "git",
    "url": "https://github.com/ThingLabsIo/IoTLabs/Arduino"
  },
  "version": "0.1.0",
  "private":true,
  "description": "Sample app that demonstrates a device connected to a gateway",
  "main": "lab02.js",
    "author": "YOUR NAME",
  "license": "MIT",
  "dependencies": {
    "johnny-five": "0.8.104"
  }
}
{% endhighlight %}

With the _package.json_ file created you can use NPM to pull down the necessary Node modules. Open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following commands (replace _C:\Development\IoTLabs_ with the path that leads to your labs folder):

If you want to compare your code to the final solution you can see the code in GitHub [here](https://github.com/ThingLabsIo/IoTLabs/blob/master/Arduino/Labs01_03/lab02.js).

<pre>
cd C:\Development\IoTLabs
npm install
</pre>

Next you will create the application code to make the LED turn on and off. Create another file in the same directory named __lab02.js__.

The first thing you need to do is define the objects you will be working with in the application. The three things that matter are a Johnny-Five framework object, an object to represent the Arduino, and the output pin the LED will be connected to.

{% highlight javascript %}
// Define the Johnny Five component
var five = require ("johnny-five"); 
// Define the pin that is connected to the LED 
var LEDPIN = 13;

// Create a Johnny Five board instance to represent your Arduino.
// Board is simply an abstraction of the physical hardware, whether it is 
// an Arduino, Raspberry Pi, Particle Photon, or other boards. 
var board = new five.Board();
{% endhighlight %}

In this code you define three variables that you will be working with:

1. <code>five</code> - represents the Johnny Five framework capabilities, which provide a type of object model for working with boards like Arduino, Raspberry Pi, and Particle.
3. <code>LEDPIN</code> - a variable that references pin __13__, which you will connected the LED to.
4. <code>board</code> - a representation of the physical board you are using.

Now that the objects are created, you can get to the meat of the application. The board object emits a 'ready' event. You will create a callback function for that event that will be invoked when the board is on, initialized and ready for action. Inside the anonymous callback function is where your application code executes (this function is invoked when the board is ready for use).

Johnny-Five provides a collection of objects that represent the board, the pins on the board, and various types of sensors and devices that could be connected to the board. For this lab you are going to write code that is fairly true to the base Arduino C/sketch programming model 
(we'll get into the abstractions that Johnny-Five provides you later). This will help you understand some of the basic concepts for how an Arduino works.

In the following code you will create a callback function that is invoked when the Arduino is initialized and ready (this is a Johnny-Five concept). You will set digital pin _13_ (the <code>LEDPIN</code> variable above) as an output pin (vs. an input pin), meaning the application is expecting to send voltage out from the pin as opposed to read the voltage coming in to the pin. Then you will create a loop that runs once per second and inside that loop you will write out to the pin either LOW or HIGH voltage. Since pin _13_ is a digital pin, its only options are 0 and 1 - in the world of Arduino-based boards that is LOW and HIGH. When you send 0 (or LOW) to the pin, that is equivalent to off (sending no voltage). When you send 1 (or HIGH) to the pin that is equivalent to on (sending full voltage).

{% highlight javascript %}
// The board.on() executes the anonymous function when the
// board reports back that it is initialized and ready. 
board.on("ready", function() {
  console.log("Board connected...");
  
  // Set the pin you connected to the LED to OUTPUT mode
  this.pinMode(LEDPIN, five.Pin.OUTPUT);
  
  // Create a loop to "flash/blink/strobe" an led
  var val = 0;
  
  this.loop( 1000, function() {
    this.digitalWrite(LEDPIN, (val = val ? 0 : 1));
  });
});
{% endhighlight %}
  
Johnny-Five actually has an object model for an LED and we could also have simply done the following, but it is important that you to see how  the <code>digitalWrite()</code> function works before abstracting it away. An alternative to the above code is:

{% highlight javascript %}
// The board.on() executes the anonymous function when the
// board reports back that it is initialized and ready. 
board.on("ready", function() {
  console.log("Board connected...");
  
  var led = new five.Led(LEDPIN);
  led.blink(1000);
});
{% endhighlight %}

## Run the App
When you run the application it will execute on your computer (the gateway), and thanks to Johnny-Five, it will connect with your board and work directly with it.

Open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following commands (replace c:\Development\IoTLabs with the path that leads to your labs folder):

<pre>
cd C:\Development\IoTLabs
node lab02.js
</pre>

You should see some lights on the board blink a little as the app is initialized, and then the on-board LED and the green LED you connected should start blinking in unison at one blink per second.

Press <kbd>CTRL</kbd> + <kbd>C</kbd> twice then <kbd>Enter</kbd>to exit the program without closing the window.

# Reading Analog Input
In this lab you will use two resistors - a static resistor and a variable resistor - to create a voltage divider that enables you to effectively understand the intensity of light detected by a photoresistor - essentially a light meter. In the previous lesson you learned how to send OUTPUT and in this lesson you will learn to collect INPUT.

## Wiring a Voltage Divider
The first step is to wire up the Arduino to read voltage as determined by the resistance created by the photoresistor. Wire your board according to the diagram (wire colors don't matter, but help with identification of purpose).

The A0-A5 pins on the board enable you to read from or write to analog sensors, such as photoresistors, knobs (potentiometers), and temperature sensors. Here is the description of the analog pins from the Arduino website:

<blockquote>
The Arduino board contains a 6 channel, 10-bit analog to digital converter. This means that it will map input voltages between 0 and 5 volts into integer values between 0 and 1023. This yields a resolution between readings of: 5 volts / 1024 units or, .0049 volts (4.9 mV) per unit.
</blockquote>

A _photoresistor_, also known as light-dependent resistor (LDR) or a photocell, works by limiting the amount of voltage that passes through it based on the intensity of light detected. The resistance decreases as light input increases - in other words, the more light, the more voltage passes through the photoresistor.

In order to take advantage of the photoresistor you will create a voltage divider - a passive linear circuit that splits the input voltage amongst two or more components (similar to a Y-splitter).

<img src="/images/lab02_schem.png"/>

To create the voltage divider needed for this lesson you will:

- Connect the voltage from the 5-volt (input voltage) pin to a circuit (using a breadboard).
- Connect the input voltage to a static resistor (10k Ohm).
- Establish a voltage divider coming out of the static resistor:
   - One route to the analog pin (A0).
   - One route to a variable resistor (the photoresistor).
- Completing the circuit out of the dynamic resistor to ground.

As the photoresistor increases its resistance (lower light intensity) more of the input voltage coming out of the 10k Ohm resistor is blocked and diverted to the A0 pin. That means that the less intense the light into the photoresistor the more resistance it creates, which in turn diverts more voltage to the A0 pin (the voltage has to go somewhere). Likewise, the more intense the light into the photoresistor, the less resistance it creates, which in turn means there is less voltage to divert to the A0 pin.

In short, the more voltage to the A0 pin, the darker it is.

Here are the specific wiring instructions.

<img src="/images/lab02_bb.png"/>

### Photoresistor
Insert a photoresistor into the breadboard as shown in the diagram.

### Resistor
Connect a 10k-Ohm resistor from one side of the photoresistor across a couple of rows.

### Wires
Connect the wires as shown in the diagram:

#### Red
Connect the 5V pin to the red/positive side-rail on the breadboard. Connect the red/positive side-rail to the row where the resistor lead is connected but the photoresistor is not (this is the input voltage into the static resistor part of the voltage divider).

#### Green
Connect the green wire from the other side of the static resistor (this should be in the same row as the static resistor lead and one of the photoresistor leads) to the A0 pin on the Arduino (this is one route of the voltage divider - the other route is through the photoresistor).

#### Black
Connect the row holding the other lead from the photoresistor to the black/negative side-rail on the breadboard. Connect the black/negative side-rail of the breadboard to the GND pin on the Arduino. This completes the circuit.

<blockquote>
Note: You could connect the 5V pin directly to the same row as the lone lead of the static resistor and the GND directly to the lead of the photoresistor, but I like building a habit of connecting the input voltage and ground pins from the Arduino to the side rails. This will come in handy in the future lessons.
</blockquote>

## Writing the Code
For this lab we will create a new file named <strong>lab02.js</strong> in the same directory as you did in Lab 1. There are no additional dependencies, so you don't need to make any changes to the package.json file.

In the _lab02.js_ file start by declaring the key objects, including a variable for the analog pin number you will use (A0 or 0).

{% highlight javascript %}
var five = require("johnny-five");
var board = new five.Board();
var ANALOGPIN = 0;
{% endhighlight %}

Next, define the callback function in the Johnny-Five board initialization. For this lab you will use the analogRead() function, which takes the analog pin number in as input and calls a callback function when input is read off the pin. In the callback function, simply write the data to the console.

{% highlight javascript %}
board.on("ready", function() {
    // read the input on analog pin 0:
    this.analogRead(ANALOGPIN, function(voltage) {
        console.log(voltage * (5.0 / 1024.0));
    });
});
{% endhighlight %}

In this case, a value for the voltage coming in to the pin from the voltage divider is passed into the callback method. The value passed in is not the actual voltage, but rather a value from 0-1023 that represents the voltage. Since you are using a 5V power circuit you have to multiple the voltage value by 1024th of 5V (or 5.0 / 1024). The result is the actual voltage being read off of the analog pin.

If you want to compare your code to the final solution you can see the code in GitHub [here](https://github.com/ThingLabsIo/IoTLabs/blob/master/Arduino/Labs01_04/lab03.js).

## Run the Application
To run the application, plug the Arduino into your computer with the USB cable. Open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following commands (replace _C:\Development\IoTLabs_ with the path that leads to your labs folder):

<pre>
cd C:\Development\IoTLabs
node lab02.js
</pre>

You should see some lights on the board blink a little as the app is initialized, and then you should see something like the following in the terminal/console window (the actual values will depend on how much light the photoresistor is receiving):

<pre>
C:\Development\IoTLabs>node lab02.js
1429351937758 Device(s) COM3
1429351937782 Connected COM3
1429351942831 Repl Initialized
>> 1.69921875
1.708984375
1.708984375
1.69921875
1.69921875
1.708984375
1.7041015625
1.6943359375
1.7041015625
</pre>

While the application is running and you are seeing data in the console, try covering the photoresistor (thus decreasing the light and increasing the resistance from the photoresistor and pushing more voltage to pin A0) or shining a light on the photoresistor (thus increasing the light and decreasing the resistance from the photoresistor and allowing more voltage through to ground (effectively stealing voltage away from the A0 pin).

Press <kbd>CTRL</kbd> + <kbd>C</kbd> twice then <kbd>Enter</kbd>to exit the program without closing the window.

# Input Controls Output

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
Congratulations! You made a device that both detects its environment and responds to it. You learned about Pulse Width Modulation and the analogWrite() function for simulating analog behavior on a digital device.

You are now ready for the [Connected Thing Labs][nextlab].

[nextlab]: /lang/js/azure/
[uno]: http://www.arduino.cc/en/Main/ArduinoBoardUno
[yun]: http://www.arduino.cc/en/Main/ArduinoBoardYun
[arduino]: http://www.arduino.cc