---
layout: page-fullwidth
title: "Writing Digital Output"
subheadline: "Particle Photon IoT Lab 1"
teaser: "In this lab you will write to a digital output."
show_meta: true
comments: true
header: no
breadcrumb: true
categories:
    - iot-photon-labs
    - maker-101
permalink: "/photon/01/"
---
If you haven't already done so, please follow the instructions in [Lab 00: Getting Started](/photon/00/) section.

### Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will use one of the digital output pins to send a signal to an LED. You will use digital pin D7, which also has an LED on the board connected to it (so you will be able to see it turn on and off even if you mis-wire something). While this lesson could be done using only the onboard LED, one of the objectives of this lab is to familiarize you with connecting input and output devices (an LED is an output device) to the board and controlling them with software.

## Bill of Materials
What you will need:

1. [Particle Photon][1]
2. USB to micro-USB cable (there is one included in the [Photon Development Kit][1])
3. LED (there is one included in the [Photon Development Kit][1])
4. 220-Ohm 1/4 Watt resistor (there is one included in the [Photon Development Kit][1])

For this lab series you are using a Particle Photon, a small Wi-Fi enabled development board. The Photon is an excellent prototyping board for connected Things and Particle, the makers of the Photon, sell the P0 chip that drives the board (so when you are ready to go into production you can easily use the same chip). While you can develop for the Photon using Particle Build or Particle Dev and leverage the Particle Cloud, this lab series is designed to teach you how to build a Wi-Fi based hub-and-spoke system, similar to how SmartThings or Phillips Hue works. For the first few labs you will learn how to create Node.js applications that run on your PC and control the Photon. Later in the lab you will learn how to deploy the Node.js applications to a hub device, like a Raspberry Pi 2 or an Arduino Y&uacute;n, which will act as the field gateway for all of the connected Photons in your solution.

## Claim Your Photon
Associating a Particle Photon to your account is known as claiming (you are claiming the Photon so no one else can claim it - its like techno-dibbs). If you refer to the Particle website there is information about how to [claim the Photon using either an iPhone or an Android phone](https://docs.particle.io/guide/getting-started/start/photon/#step-1-power-on-your-device), or [over USB from Windows or OS X](https://docs.particle.io/guide/getting-started/connect/photon/). As of the writing of this tutorial, there is a bug in the USB claiming process for Windows 10. After following the steps to install the Particle USB driver, use the following steps to claim the Photon to your account if you are on Windows 10, or if you experience difficulties on previous versions of Windows.

Open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following command to get the device ID of your Photon:

<pre>
  particle identify  
</pre>

If the Photon is connected correctly and working you shold see a result similar to this:
<pre>
  Your device id is 21003f000447343335555555
</pre>

Copy the device ID somewhere you can reference it in a momment. Next, enter the following to connect the Photon to your Wi-Fi network:

<pre>
  c:\Development\gh\IoTLabs>particle serial wifi
</pre>

You should be prompted to scan for Wi-Fi networks.
<pre>
  ? Should I scan for nearby Wi-Fi networks? (Y/n)
</pre>

Enter <code>Y</code> and press <kbd>Enter</kbd> (if you choose <code>N</code> you will need to manually identify the Wi-Fi network using its SSID). You should get a list of available Wi-Fi networks:

<pre>
c:\Development\gh\IoTLabs>particle serial wifi
? Should I scan for nearby Wi-Fi networks? (Y/n) y
? Should I scan for nearby Wi-Fi networks? Yes
? Select the Wi-Fi network with which you wish to connect your device: (Use arrow keys)
> MyHome
  Terminus
  JimsHouse
</pre>

Use the down arrow key to highlight the SSID for the Wi-Fi network you want to connect to and press <kbd>Enter</kbd>. You should be prompted to identify the seurity protocol (e.g. WPA2, WPA, WEP, Unsecured). Choose the correct option for your Wi-Fi network and press <kbd>Enter</kbd>, then enter the Wi-Fi network password when prompted.

<pre>
  c:\Development\gh\IoTLabs>particle serial wifi
  ? Should I scan for nearby Wi-Fi networks? (Y/n) y
  ? Should I scan for nearby Wi-Fi networks? Yes
  ? Select the Wi-Fi network with which you wish to connect your device: (Use arro? Select the Wi-Fi network with which you wish to connect your device: Terminus
  ? Security Type: WPA2
  ? Wi-Fi Password: RickGrimes
  Attempting to configure Wi-Fi on COM4
  I said: w
  Serial said: SSID:
  I said: Terminus
  
  Serial said: Terminus
  Security 0=unsecured, 1=WEP, 2=WPA, 3=WPA2:
  I said: 3
  
  Serial said: 3
  Password:
  I said: RickGrimes
  
  Serial said: RickGrimes
  Thanks! Wait while I save those credentials...
  
  Done! Your device should now restart.
  
  c:\Development\gh\IoTLabs>
</pre>

Now push the <b>Reset</b> button on the Photon. After the Photon reboots the indicator LED will flash magenta. At this point the Photon has been configured to connect to your Wi-Fi network. Now you need to add the Photon to your Particle.io account (this is where you need the device ID that you copied earlier).

<pre>
  particle device add YOUR_DEVICE_ID_HERE
</pre>

If you are not already logged in to your Particle.io account, you will be prompted to login. Follow the prompts and run the above command again. You should see something like this:

<pre>
  c:\Development\gh\IoTLabs>particle device add 21003f000447343335555555
  You're not logged in. Please login using particle cloud login before using this command
  
  c:\Development\gh\IoTLabs>particle cloud login
  ? Please enter your email address: me@thinglabs.io
  ? Please enter your password: **********
  > Successfully completed login!
  
  c:\Development\gh\IoTLabs>particle device add 21003f000447343335555555
  Claiming device 21003f000447343335555555
  Successfully claimed device 21003f000447343335555555
  
  c:\Development\gh\IoTLabs>
</pre>

At this point your Photon may blick magenta on and off for a few seconds (or up to a minute) as it gets any core firmware updates available for it. When the blinking light turns to a 'breathing' cyan then your Photon is ready for use (the 'breathing' cyan is the ready state indicator).
 
## Upload the VoodooSpark Firmware to Your Photon
For this lab series you will be controlling the Photon from a master/hub device (your PC for the early parts of the labs series, and a Raspberry Pi 2 for the later parts). The controller application will use Node.js and a framework called Johnny Five. In order for these tools to communicate with the Photon, it needs to have the VoodooSpark firmware installed. The VoodooSpark firmware enables direct TCP communication from the host machine (your PC running the Node.js app) and the Photon (provided they are on the same Wi-Fi network). Installing the VoodooSpark firmware is fairly easy to do using the Particle Build web-based IDE.

1. In a browser open the [VoodooSpark code from GitHub](https://raw.githubusercontent.com/voodootikigod/voodoospark/master/firmware/voodoospark.cpp).
2. With your Photon still powered on, use a second browser tab/window to open [Particle Build](https://build.particle.io/).
3. In Particle Build, click on the code icon in the lower-left toolbar (it looks like opening and closing angle brackets &lt; &gt;).
4. Click on the _Create New App_ button.
5. Name the app "VoodooSpark"
6. Copy all of the code from the VoodooSpark GitHub page to your new app (be sure to replace any code in the new app).
7. Click on the _Devices_ icon in the lower-left toolbar (it looks like a targeting sight).
8. Find your new Photon (there will likely be a device with no name/alias with the text 'No TItle (click to edit)' - that is your new Photon - feel free to give it a friendly name at this time).
9. Click on the gray star that appears next to the Photon when you hover over it to turn it yellow (doing this selects this device as the target for a firmware flash).
10. Click on the verify icon in the upper-left toolbar (it looks like a checkmark). In the lower-left corner of the IDE you will see the verification status.
11. After the app is verified, click the flash firmware icon in the upper-left toolbar (it looks like a lightening bolt) to flash your Photon with the VoodooSpark firmware.

After completing the last step you should see the indicator LED on your Photon begin to blink. When it returns to the 'breathing' cyan the update is complete.

## Wiring the LED
Next you need to wire up the Photon board so that it can send <code>ON</code> and <code>OFF</code> commands to the LED. You can wire your board according to the diagram (wire colors don't matter, but help with identification of purpose).

<img src="/images/photon_lab01_bb.png"/>

### LED
Insert a LED into the breadboard as shown in the diagram, connecting the positive lead to the D7 pin and inserting the negative lead into the negative (-) side rail. For reference, an LED has one lead that is longer than the other. The longer lead is the positive (+) lead, and the shorter lead is the negative (-) lead.

### Resistor
Connect a 2200-Ohm resistor from the negative (-) side rail to the GND pin.

## Write the Code
Since we are using Node.js and Johnny-Five for this lab we can take advantage of the dependency management capabilities that Node.js provides. We need to let our application know that it has a dependency on the Johnny-Five framework as well as the Spark-IO framework so that when the application is prepared for execution, it can fetch the required dependencies for us. In Node.js this is done with a package.json file. This file provides some basic meta-data about the application, including any dependencies on packages that can be retrieved using NPM (according to [npmjs.com](https://www.npmjs.com) today, NPM stands for Narrating Prophetic Monks...not Node Package Manager like you may have thought).

Using your favorite/preferred text/code editor, create a file in your labs folder named <strong>package.json</strong> and add the following:

{% highlight javascript %}
{
  "name": "IoT-Labs-Photon",
  "repository": {
    "type": "git",
    "url": "https://github.com/ThingLabsIo/IoTLabs/Photon"
  },
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "johnny-five": "^0.8.0",
    "spark-io": "^0.6.1"
  }
}
{% endhighlight %}

With the package.json file created you can use NPM to pull down the necessary Node modules. Open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following commands (replace _C:\Development\IoTLabs_ with the path that leads to your labs folder):

<pre>
  cd C:\Development\IoTLabs
  npm install
</pre>

Next you will create the application code to make the LED turn on and off.

Create another file in the same directory named <strong>lab01.js</strong>.

The first thing you need to do is define the objects you will be working with in the application. The three things that matter are a Johnny-Five framework object, an object to represent the Photon, and the output pin the LED will be connected to. In order to complete this step you will need the device ID you copied earlier when you were claiming the Photon (or the name/alias you gave the Photon when you updated the firmware to VoodooSpark) and your Particle Cloud access token. To get the access token, Open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following command (you may be prompted to login or provide your Particle Cloud password again):

<pre>
  particle token list
</pre>

Find the token for _user_ (make sure if you see more than one that you choose the one that is not expired).

Now add the following code to the <b>lab01.js</b> file:

{% highlight javascript %}
// Define the Jonny Five and Spark-IO variables
var five = require("johnny-five");
var spark = require("spark-io");
// Define the Johnny Five board as your Particle Photon
var board = new five.Board({
  io: new spark({
    token: process.env.PARTICLE_KEY || 'YOUR API KEY HERE',
    deviceId: process.env.PARTICLE_DEVICE || 'YOUR DEVICE ID OR ALIAS HERE'
  })
});
// Define the pin that is connected to the LED
var LEDPIN = "D7";
{% endhighlight %}

In this code you define four variables that you will be working with:

1. <code>five</code> - represents the Johnny Five framework capabilities, which provide a type of object model for working with boards like Arduino and Particle.
2. <code>spark</code> - represents the Spark IO framework capabilities which exposes the specifics of the Particle boards inclusing the Core and the Photon.
3. <code>board</code> - a representation of the physical board you are using. This is created by specifying the Spark board and passing in the Particle Cloud API token and the device ID.
4. <code>LEDPIN</code> - a variable that references pin D7, which you will connected the LED to.

Now that the objects are created, you can get to the meat of the application. Johnny-Five provides a board 'ready' construct that makes a callback when the board is on, initialized and ready for action. Inside the ananymous callback function is where your application code executes (this function is invoked when the board is ready for use).

Johnny-Five provides a collection of objects that represent the board, the pins on the board, and various types of sensors and devices that could be connected to the board. For this lab you are going to write code that is fairly true to the base Arduino C/sketch or Particle Build programming model (we'll get into the abstractions that Johnny-Five provides you later). This will help you understand some of the basic concepts for how an Arduino or Particle board works.

In the following code you will create a callback function that is invoked when the Photon is initialized and ready (this is a Johnny-Five concept). You will set digital pin _D7_ (the <code>LEDPIN</code> variable above) as an output pin (vs. an input pin), meaning the application is expecting to send voltage out from the pin as opposed to read the voltage coming in to the pin. Then you will create a loop that runs once per second and inside that loop you will write out to the pin either LOW or HIGH voltage. Since pin _D7_ is a digital pin, its only options are 0 and 1 - in the world of Arduino-based boards that is LOW and HIGH. When you send 0 (or LOW) to the pin, that is equivalent to off (sending no voltage). When you send 1 (or HIGH) to the pin that is equivalent to on (sending full voltage).

{% highlight javascript %}
// The board.on() executes the anonymous function when the 
// Partile Photon reports back that it is initialized and ready.
board.on("ready", function(){
  // Set the pin you connected to the LED to OUTPUT mode
  this.pinMode(LEDPIN, five.Pin.OUTPUT);
  // Create a loop to "flash/blink/strobe" an led
  var val = 0;
  this.loop( 1000, function() {
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

## Run the App
When you run the application it will execute on your computer, and thanks to Johnny-Five, it will connect with your board and work directly with it. Basically, your computer is acting as a hub and communicating via Wi-Fi with the Photon as one of potentilly many devices (or spokes). In a future lab you will deploy the Node.js application to another device (like a Rasberry Pi 2) which will act as the hub and connect to multiple spoke devices.

Open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following commands (replace c:\Development\IoTLabs with the path that leads to your labs folder):

<pre>
  cd C:\Development\IoTLabs
  node lab01.js
</pre>

You should the indicator LED on the Photon blink a little as the app is initialized, and then the on-board LED next to pin _D7_ and the LED you connected should start blinking in unison at one blink per second.

<blockquote>
Note: The Photon doesn't need to be plugged into your PC - it simply needs to be powered on and on the same Wi-Fi you configured it for (and the PC running the Node.js app has to be on the same Wi-F network). 
</blockquote>
  
Press <kbd>CTRL</kbd> + <kbd>C</kbd> twice to exit the program without closing the window. After stopping the application press the _Reset_ button on the Photon to prepare it for the next run. 

## Conclusion &amp; Next Steps
In this lab you learned how to write a Node.js/Johnny-Five application that writes LOW and HIGH signals to a digital pin (designated for output) to make an LED blink. In itself this may not be very exciting, but the core concept is necessary - writing to a digital output pin.

In the [next lab][2] you will learn how to read voltage coming in on an analog pin, and you will learn how to use a voltage divider to capture the variable resistance provided by a photoresistor.

[Next Lab ->][2]

{% include next-previous-post-in-category.html %}

[1]: https://store.particle.io/?product=photon-kit
[2]: /photon/02/