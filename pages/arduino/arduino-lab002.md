---
layout: page-fullwidth
title: "Writing Digital Output"
subheadline: "Arduino + Azure IoT Lab 2"
teaser: "In this lab you will write to a digital output."
show_meta: true
comments: true
header: no
breadcrumb: true
categories:
    - iot-arduino-labs
    - maker-101
permalink: "/arduino/02/"
---
If you haven't already done so, please follow the instructions in [Lab 00: Getting Started][gettingstarted] section.

### Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will use one of the digital output pins to send a signal to an LED. You will use digital pin 13, which also has an LED on the board connected to it (so you will be able to see it turn on and off even if you mis-wire something). While this lesson could be done using only the onboard LED, one of the objectives of this lab is to familiarize you with connecting input and output devices (an LED is an output device) to the board and controlling them with software.

## Bill of Materials
What you will need:

1. [Arduino Uno][uno] or [Arduino Y&uacute;n][yun] 
2. USB cable to connect the Arduino to your computer (the type will vary depending on the Arduino board you are using)
3. [5mm Green LED][2]
4. [330-Ohm 1/4 Watt resistor][3] (Orange-Orange-Brown)

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
Since we are using Node.js and Johnny-Five for this lab we can take advantage of the dependency management capabilities that 
Node.js provides. We need to let our application know that it has a dependency on the Johnny-Five framework so that when the 
application is prepared for execution, it can fetch the required dependencies for us. In Node.js this is done with 
a _package.json_ file. This file provides some basic meta-data about the application, including any dependencies on packages 
that can be retrieved using the Node Package Manager (NPM).

Using your favorite/preferred text/code editor, create a file in your labs folder named <strong>package.json</strong> and add the following:

{% highlight javascript %}
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
    "johnny-five": "^0.8.104"
  }
}
{% endhighlight %}

With the _package.json_ file created you can use NPM to pull down the necessary Node modules. Open a terminal window (Mac OS X) or Node.js 
command prompt (Windows) and execute the following commands (replace _C:\Development\IoTLabs_ with the path that leads to your labs folder):

If you want to compare your code to the final solution you can see the code in GitHub [here][lab02code].

<pre>
cd C:\Development\IoTLabs
npm install
</pre>

Next you will create the application code to make the LED turn on and off. Create another file in the same directory named <strong>lab02.js</strong>.

The first thing you need to do is define the objects you will be working with in the application. The three things that matter are a Johnny-Five 
framework object, an object to represent the Arduino, and the output pin the LED will be connected to.

{% highlight javascript %}
// Define the Johnny Five and Particle-IO variables
var five = require ("johnny-five"); 
// Define the pin that is connected to the LED 
var LEDPIN = 13;

// Create a Johnny Five board instance to represent your Arduino.
// Board is simply an abstraction of the physical hardware, whether it is 
// a Arduino, Raspberry Pi, Particle Photon, or other boards. 
var board = new five.Board();
{% endhighlight %}

In this code you define three variables that you will be working with:

1. <code>five</code> - represents the Johnny Five framework capabilities, which provide a type of object model for working with boards like Arduino, Raspberry Pi, and Particle.
3. <code>LEDPIN</code> - a variable that references pin __13__, which you will connected the LED to.
4. <code>board</code> - a representation of the physical board you are using.

Now that the objects are created, you can get to the meat of the application. The board object emits a 'ready' event. You will create a 
callback function for that event that will be invoked when the board is on, initialized and ready for action. Inside the ananymous callback 
function is where your application code executes (this function is invoked when the board is ready for use).

Johnny-Five provides a collection of objects that represent the board, the pins on the board, and various types of sensors and devices that 
could be connected to the board. For this lab you are going to write code that is fairly true to the base Arduino C/sketch programming model 
(we'll get into the abstractions that Johnny-Five provides you later). This will help you understand some of the basic concepts for how an 
Arduino works.

In the following code you will create a callback function that is invoked when the Arduino is initialized and ready (this is a Johnny-Five concept). 
You will set digital pin _13_ (the <code>LEDPIN</code> variable above) as an output pin (vs. an input pin), meaning the application is expecting 
to send voltage out from the pin as opposed to read the voltage coming in to the pin. Then you will create a loop that runs once per second and 
inside that loop you will write out to the pin either LOW or HIGH voltage. Since pin _13_ is a digital pin, its only options are 0 and 1 - in the 
world of Arduino-based boards that is LOW and HIGH. When you send 0 (or LOW) to the pin, that is equivalent to off (sending no voltage). When you 
send 1 (or HIGH) to the pin that is equivalent to on (sending full voltage).

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
  
Johnny-Five actually has an object model for an LED and we could also have simply done the following, but it is important that you to see how 
the <code>digitalWrite()</code> function works before abstracting it away. An alternative to the above code is:

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
When you run the application it will execute on your computer (the gateway), and thanks to Johnny-Five, it will connect with your board and 
work directly with it.

Open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following commands (replace c:\Development\IoTLabs with 
the path that leads to your labs folder):

<pre>
cd C:\Development\IoTLabs
node lab02.js
</pre>

You should see some lights on the board blink a little as the app is initialized, and then the on-board LED and the green LED you connected 
should start blinking in unison at one blink per second.

Press <kbd>CTRL</kbd> + <kbd>C</kbd> twice then <kbd>Enter</kbd>to exit the program without closing the window.

## Conclusion &amp; Next Steps
In this lab you learned how to write a Node.js/Johnny-Five application that writes LOW and HIGH signals to a digital pin (designated for output) 
to make an LED blink. In itself this may not be very exciting, but the core concept is necessary - writing to a digital output pin.

In the [next lab][nextlab] you will learn how to read voltage coming in on an analog pin, and you will learn how to use a voltage divider to capture the 
variable resistance provided by a photoresistor.

[Next Lab ->][nextlab]

## Want to Try Something?
If you want to experiment with more labs like this you can try out these code samples:

1. [Writing out to a RGB LED][lab02rgb]
2. [Writing out to a Servo][lab02servo]

{% include next-previous-post-in-category.html %}

[uno]: http://www.arduino.cc/en/Main/ArduinoBoardUno
[yun]: http://www.arduino.cc/en/Main/ArduinoBoardYun
[1]: http://www.arduino.cc/en/Main/ArduinoBoardYun
[2]: http://www.sparkfun.com/products/12062
[3]: http://www.sparkfun.com/products/10969
[4]: http://www.sparkfun.com/products/13154
[nextlab]: /arduino/03/
[gettingstarted]: /arduino/00/
[lab02rgb]: https://github.com/ThingLabsIo/IoTLabs/blob/master/Arduino/Labs01_03/lab02_rgb.js
[lab02servo]: https://github.com/ThingLabsIo/IoTLabs/blob/master/Arduino/Labs01_03/lab02_servo.js
[lab02code]: https://github.com/ThingLabsIo/IoTLabs/blob/master/Arduino/Labs01_03/lab02.js