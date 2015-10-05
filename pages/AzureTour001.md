---
layout: page-fullwidth
title: "Sending Telemetry to the Cloud"
subheadline: "Microsoft Azure IoT Lab 1"
teaser: "In this lab you will gather telemetry and send it to the cloud."
show_meta: true
comments: true
header: no
breadcrumb: true
categories:
    - iot-photon-labs
    - maker-101
author: "Doug Seven"
permalink: "/azure/01/"
---
### Table of Contents
*  Auto generated table of contents
{:toc}

If you haven't already done so, please follow the instructions in [Lab 00: Getting Started](/azure/00/) section.

### Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will create a small device that collects environmental telemetry (i.e. temperature, humidity) and send it to the cloud. THis is the first setp in building an IoT solution.

## Bill of Materials
What you will need:

1. [Particle Photon][1]
2. USB to micro-USB cable (there is one included in the [Photon Development Kit][1])
3. [SparkFun Weather Shield for Particle Photon - $32.95](https://www.sparkfun.com/products/13630) _TEMP EDIT: we will use the TMP36 tempurature sensor in this lab until the new sensor capabilities are added to Johnny Five_

For this lab series you are using a Particle Photon, a small Wi-Fi enabled development board. The Photon is an excellent prototyping board for connected Things and Particle, the makers of the Photon, sell the P0 chip that drives the board (so when you are ready to go into production you can easily use the same chip). While you can develop for the Photon using Particle Build or Particle Dev and leverage the Particle Cloud services, this lab series is designed to teach you how to build a Wi-Fi based hub-and-spoke system, similar to how SmartThings, Lowes Iris, or Phillips Hue works. For the first few labs you will learn how to create Node.js applications that run on your PC and control the Photon. If you choose to continue with these labs on your own, you will learn how to deploy the Node.js applications to a hub device, like a Raspberry Pi or an Arduino Y&uacute;n, which will act as the field gateway for all of the connected _Things_ in your solution.

## Claim Your Photon
Associating a Particle Photon to your Particle account is known as _claiming_ (you are claiming the Photon so no one else can claim it - its like high-tech cookie-licking). If you refer to the Particle website there is information about how to [claim the Photon using either an iOS or Android device](https://docs.particle.io/guide/getting-started/start/photon/#step-1-power-on-your-device), or [over USB from Windows or OS X](https://docs.particle.io/guide/getting-started/connect/photon/). 

<blockquote>
  As of the writing of this tutorial, there is a bug in the USB claiming process for Windows 10. After following the steps to install the Particle USB driver, use the following steps to claim the Photon to your account if you are on Windows 10, or if you experience difficulties on previous versions of Windows.
</blockquote>

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

Now push the __Reset__ button on the Photon. After the Photon reboots the indicator LED will flash magenta. At this point the Photon has been configured to connect to your Wi-Fi network. Now you need to add the Photon to your Particle.io account (this is where you need the device ID that you copied earlier).

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

At this point your Photon may blink magenta on and off for a few seconds (or up to a minute) as it gets any core firmware updates available for it. When the blinking light turns to a 'breathing' cyan then your Photon is ready for use (the 'breathing' cyan is the ready state indicator).
 
## Upload the VoodooSpark Firmware to Your Photon
For this lab series you will be controlling the Photon from a master/hub device (during development this will be your laptop). The controller application will use Node.js and a framework called Johnny Five. In order for these tools to communicate with the Photon, it needs to have the VoodooSpark firmware installed. The VoodooSpark firmware enables direct TCP communication from the host machine (your laptop running the Node.js app) and the Photon (provided they are on the same Wi-Fi network). Installing the VoodooSpark firmware is fairly easy to do using the Particle Build web-based IDE.

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

## Connecting the Weather Shield
The weather shield comes equiped with two onboard sensors - a HTU21D humidity sensor capable of gathering both humidity and temperature, and a MPL3115A2 barometric pressure sensor. THere are connections for other optional sensors, such as wind, rain and soile moisture sensors (we will not be working with the optional sensors in this lab series). Simply plug the Photon into the weather shield so that the USB port on the Photon is on the same side as the RJ-11 plugs on the weather shield. 

<img src="/images/Photon_Weather_Shield.jpg"/>

### TEMP - Wire up the TMP36 Temperature Sensor
Until the Weather Sheild is supported in Johnny Five we will use the common TMP36 sensor. With the flat side facing you, connect the left lead to the 3.3v pin, the center lead to the A0 pin, and the right lead to the GND pin.

<img src="/images/photon_tmp36_bb.png"/>
  
## Setup an Azure IoT Hub

In a browser, navigate to https://portal.azure.com. Login to the account you created in in [Lab 00: Getting Started](/azure/00/). Once logged in:

1. Click on the _New_ menu option in the upper-left
2. Select _Internet of Things_
3. Select _Azure IoT Hub_
4. Give it a name such as your name followed by 'iot-labs' (i.e. rickgrimes-iot-labs)
5. Select or create a new Resource Group
6. Select a location (choose the one closest to your pysical location)

<img src="/images/New-IoT-Hub.png"/>
  
Once the IoT Hub is created, navigate into it and:

1. Click on the key icon at the top of the blade
2. In the next blade, click on the _iothubowner_ entry
3. Copy the _Connection string-primary key_ to your clipboard

<img src="/images/AzureIoTConnectionString.png"/>

## Create a New Azure IoT Device

On Windows, open the Node.js command prompt and type the following:
<pre>
  cd c:\Development\node_modules\iothub-explorer
  npm install
</pre>

On Mac OS X open Terminal and type the following:
<pre>
  cd ~/Development/IoTLabs/node_modules/iothub-explorer
  sudo npm install
</pre>

After the dependencies are installed, you can execute a _create_ command to create a new device in your Azure IoT Hub. WHen you are using the iothub-explorer utility you must provide the connection string you copied above.

In the same directory as before, using the Node.js command prompt or Terminal:

<pre>
  node iothub-explorer.js [YOUR CONNECTION STRING] create [YOUR PHOTON DEVICE ID/ALIAS]
</pre>

Once a device is created, you can get the device-specific connection string with the following command:

<pre>
  node iothub-explorer.js [YOUR CONNECTION STRING] get [YOUR PHOTON DEVICE ID/ALIAS] --connection-string
</pre>

The device-specific connection string identifies the device by name and includes a key that is only for that device. Copy the device connection string somewhere that you will be able to access it shortly.

<blockquote>
  Optionally you can download and run a small Windows utility app called [Device Explorer](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md). This utility is configured witht he same connection string you used for the iothub-explorer utility, but it has a graphical UI instead of a command-line interface, however; it is limited to running on Windows. 
</blockquote>

## Write the Code
Since you will be using Node.js for this lab you can take advantage of the dependency management capabilities that Node.js and NPM provide. You need to let your application know that it has a dependency on the following NPM packages - Azure IoT Device, Johnny-Five and Particle-IO. In Node.js this is done with a _package.json_ file. This file provides some basic meta-data about the application, including any dependencies on packages that can be retrieved using NPM (according to [npmjs.com](https://www.npmjs.com) today, NPM stands for Narrating Prophetic Monks...not Node Package Manager like you may have thought).

Using your favorite/preferred text/code editor, create a file in your development directory named __package.json__ and add the following:

{% highlight javascript %}
{
  "name": "IoT-Labs",
  "version": "0.1.0",
  "private":true,
  "description": "Sample app that connects a device to Azure using Node.js",
  "main": "temp.js",
    "author": "YOUR NAME HERE",
  "license": "MIT",
  "dependencies": {
    "azure-iot-device": "^1.0.0-preview.3",
    "johnny-five": "^0.8.0",
    "particle-io": "^0.10.0"
  }
}
{% endhighlight %}

With the package.json file created you can use NPM to pull down the necessary Node modules. Open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following commands (replace _C:\Development\IoTLabs_ with the path that leads to your development directory):

On Windows, open the Node.js command prompt and type the following:
<pre>
  cd C:\Development\IoTLabs
  npm install
</pre>

On Mac OS X open Terminal and type the following:
<pre>
  cd ~/Development/IoTLabs
  sudo npm install
</pre>

Next you will create the application code to gather temperature data and send it to the cloud.

Create another file in the same directory named __temp.js__.

The first thing you need to do is define the objects you will be working with in the application. The three things that matter are the Azure IoT Device object, a Johnny-Five framework object, and an object to represent the Photon. In order to complete this step you will need the device ID you copied earlier when you were claiming the Photon (or the name/alias you gave the Photon when you updated the firmware to VoodooSpark) and your Particle Cloud access token. To get the access token, Open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following command (you may be prompted to login or provide your Particle Cloud password again):

<pre>
  particle token list
</pre>

Find the token for _user_ (make sure if you see more than one that you choose the one that is not expired).

Optionally you can use a browser to navigate to [Particle Build](https://build.particle.io/) and find your Access Token on the setting page (click on the gear icon in the lower-left part of the screen).


Now add the following code to the __temp.js__ file:

{% highlight javascript %}
'use strict';
// Define the Jonny Five, Particle and Azure IoT objects
var five = require ("johnny-five");
var Particle = require("particle-io");
var device = require('azure-iot-device');

// Set up the access credentials for Particle and Azure
var particleKey = process.env.PARTICLE_KEY || 'YOUR PARTICLE ACCESS TOKEN HERE';
var deviceName = process.env.PARTICLE_DEVICE || 'YOUR PARTICLE DEVICE ID/ALIAS HERE';
var connectionString = process.env.IOTHUB_CONN || 'YOUR IOT HUB DEVICE-SPECIFIC CONNECTION STRING HERE';

// Define the Johnny Five board as your Particle Photon
var board = new five.Board({
  io: new Particle({
    token: particleKey,
    deviceId: deviceName
  })
});

var client = new device.Client(connectionString, new device.Https());
{% endhighlight %}

In this code you define four variables that you will be working with:

1. <code>five</code> - represents the Johnny Five framework capabilities, which provide a type of object model for working with boards like Arduino and Particle.
2. <code>Particle</code> - represents the Particle IO framework capabilities which exposes the specifics of the Particle boards including the Core and the Photon.
3. <code>board</code> - a representation of the physical board you are using. This is created by specifying the Particle board and passing in the Particle Access Token and the device ID or alias.
4. <code>client</code> - This is the agent class that facilitates the communication between your device and the Azure IoT Hub. It takes the device-specific connection string as an argument and establishes a connection with the IoT Hub.

Now that the objects are created, you can get to the meat of the application. Johnny Five provides a board 'ready' event that makes a callback when the board is on, initialized and ready for action. Inside the ananymous callback function is where your application code executes (this function is invoked when the board is ready for use).

Johnny Five provides a collection of objects that represent the board, the pins on the board, and various types of sensors and devices that could be connected to the board. In this lab series you will work with the Temperature and Barometer classes as a representation of the HTU21D humidity sensor and the MPL3115A2 barometric pressure sensor respectively. When you create an instance of the classes you will specfy the controller class by defining the sensor name (HTU21D or MPL3115A2).

In the following code you will create a callback function that is invoked when the Photon is initialized and ready (this is a Johnny Five concept). You will set digital pin _D7_ (the <code>LEDPIN</code> variable above) as an output pin (vs. an input pin), meaning the application is expecting to send voltage out from the pin as opposed to read the voltage coming in to the pin. Then you will create a loop that runs once per second and inside that loop you will write out to the pin either LOW or HIGH voltage. Since pin _D7_ is a digital pin, its only options are 0 and 1 - in the world of Arduino-based boards that is LOW and HIGH. When you send 0 (or LOW) to the pin, that is equivalent to off (sending no voltage). When you send 1 (or HIGH) to the pin that is equivalent to on (sending full voltage).

{% highlight javascript %}
'use strict';
var five = require ("johnny-five");
var device = require('azure-iot-device');
var Particle = require("particle-io");

var particleKey = process.env.PARTICLE_KEY || 'YOUR PARTICLE ACCESS TOKEN HERE';
var deviceName = process.env.DEVICE_NAME || 'YOUR PARTICLE PHOTON DEVICE ID/ALIAS HERE';
var location = process.env.DEVICE_LOCATION || 'THE LOCATION OF THE PARTICLE PHOTON DEVICE';
var connectionString = process.env.IOTHUB_CONN || 'YOUR IOT HUB DEVICE-SPECIFIC CONNECTION STRING HERE';

var client = new device.Client(connectionString, new device.Https());

// Create a Johnny Five board board instance to represent your Particle Photon
var board = new five.Board({
  io: new Particle({
    token: particleKey,
    deviceId: deviceName
  })
});

// hF, hC, bF, bC are holder variables for the fahrenheit and celsius values from the
// hygrometer and barometer respectively.
var hF, hC, bF, bC, relativeHumidity, pressure;

// The board.on() executes the anonymous function when the 
// board reports back that it is initialized and ready.
board.on("ready", function() {
    console.log("Board connected...");
    
    // The SparkFun Weather Shield for the Particle Photon has two sensors on the I2C bus - 
    // a humidity sensor (HTU21D) which can provide both humidity and temperature, and a 
    // barometer (MPL3115A2) which can provide both barometric pressure and humidity.
    // WHen you create objects for the sensors you use the PHOTON_WEATHER_SHILED controller
    // which is a multi-class controller. Create objects for each data type you will 
    // use by specifying the controller which maps to the specific sensor.
    var shield = new five.Multi({
        controller: "PHOTON_WEATHER_SHIELD"
    });
    
    // The shield.on function invokes the ananymous callback function at the 
    // frequency specified (250ms by default). The anonymous function is scoped
    // to the object (e.g. this == the weather shield Multi class object). 
    shield.on("data", function() {
        hF = this.hygrometer.temperature.fahrenheit;
        hC = this.hygrometer.temperature.celsius;
        relativeHumidity = this.hygrometer.hygrometer.relativeHumidity;
        
        bF = this.barometer.temperature.fahrenheit;
        bC = this.barometer.temperature.celsius;
        pressure = this.barometer.pressure;
        
        // Create a JSON payload for the message that will be sent to Azure IoT Hub
        var payload = JSON.stringify({
            deviceId: deviceName,
            location: location,
            fahrenheit: (hF + bF) /2,
            celsius: (hC + bC) / 2,
            relativeHumidity: relativeHumidity,
            pressure: pressure
        });
    
        // Create the message based on the payload JSON
        var message = new device.Message(payload);
        // For debugging purposes, write out the message paylod to the console
        console.log("Sending message: " + message.getData());
        // Send the message to Azure IoT Hub
        client.sendEvent(message, printResultFor('send'));
    });
});
    
// Helper function to print results in the console
function printResultFor(op) {
  return function printResult(err, res) {
    if (err) console.log(op + ' error: ' + err.toString());
    if (res && (res.statusCode !== 204)) console.log(op + ' status: ' + res.statusCode + ' ' + res.statusMessage);
  };
}
{% endhighlight %}

_TEMP EDIT: for this code sample you need to replace the board.on() function with one that uses the TMP36 sensor until the weather shield is supported in Johnny Five (planned by Oct-9-2015)_

{% highlight javascript %}
// The board.on() executes the anonymous function when the 
// Particle Photon reports back that it is initialized and ready.
board.on("ready", function() {
    console.log("Board connected...");
    
    var temperature= new five.Temperature({
        controller: "TMP36",
        pin: "A0",
        freq: 1000 // Gather the temperature once per second
    });

    // The temperature.on function involes the ananymous callback
    // function at the frequency specified above. The anonymous function
    // is scoped to the data returned from the sensor (e.g. fahrenheit 
    // or celsius temperatures). 
    temperature.on("data", function() {
        hF = this.F;
        hC = this.C;
    
        // Create a JSON payload for the message that will be sent to Azure IoT Hub
        var payload = JSON.stringify({
          deviceId: deviceName,
          location: location,
          fahrenheit: hF,
          celsius: hC 
        });
        
        // Create the message based on the payload JSON
        var message = new device.Message(payload);
 
        // For debugging purposes, write out the message paylod to the console
        console.log("Sending message: " + message.getData());
        
        // Send the message to Azure IoT Hub
        client.sendEvent(message, printResultFor('send'));
    });
});

// Helper function to print results in the console
function printResultFor(op) {
  return function printResult(err, res) {
    if (err) console.log(op + ' error: ' + err.toString());
    if (res && (res.statusCode !== 204)) console.log(op + ' status: ' + res.statusCode + ' ' + res.statusMessage);
  };
}
{% endhighlight %}
  
In this code you do a number of things:
1. <code>board.on()</code> - This function triggers the Photon to invoke the anonymous callback function as soon as the board is on and ready. All of the application code for the device is written inside this callback function.
2. Define <code>temperature</code> object. This is a representation of the physical sensor connected to the Photon. You instantiate it by specifying the controller class to use (this informs the framework how to interact with this sensor), the physical pin that it is sending data to (Analog pin 0 -or A0- in this example), and a frequency to report the data collected by the sensor. Many sensors are capable of collecting data in fraction of a second intervals. You may not want to collect data and send it to Azure IoT Hubs that frequently. The <code>freq</code> property defines (in milliseconds) how ofter to raise an event to report the data from the sensor. In this example you are raising the event once per second. 
3. <code>temperature.on()</code> is the function that initializes the temperature sensor controller. As soon as it is initialized it begins invoking the ananymous callback function repeatedly based on the <code>freq</code> value. Each time the data is gathered and passed ot the ananymous function you can create and send a telemetry message to Azure IoT Hub.
4. <code>message</code> is the object that represents the data you are sending to Azure IoT Hub. This is a JSON formatted message. In this example you create a message with two data points - _deviceName_ and _temperture_.

WHen <code>client.sendEvent()</code> is invoked, the JSON message is sent to Azure IoT Hub. FOr now, nothing happens with the message because you haven't set up anything that will capture the message and do something with it (we will get to that soon). 

## Run the App
When you run the application it will execute on your computer, and thanks to Johnny Five, it will connect with your Photon and work directly with it. Basically, your computer is acting as a hub and communicating via TCP over your local Wi-Fi network with the Photon as one of potentilly many devices (or spokes). If you continue on past today, in a future lab you will deploy the Node.js application to another device (like a Rasberry Pi) which will act as the hub and connect to multiple spoke devices.

Open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following commands (replace c:\Development\IoTLabs with the path that leads to your labs folder):

<pre>
  cd C:\Development\IoTLabs
  node temp.js
</pre>

After the board initializes you will see messages printing out once per second. These are the same messages being sent to Azure IoT Hub. If you downloaded the Device Explorer utility for Windows you can open the _Data_ tab, select a device, and click _Monitor_ to begin monitoring messages as they come into your Azure IoT Hub.

<blockquote>
Note: The Photon doesn't need to be plugged into your PC - it simply needs to be powered on and on the same Wi-Fi you configured it for (and the PC running the Node.js app has to be on the same Wi-F network). 
</blockquote>

When you want to quite the application, press <kbd>CTRL</kbd> + <kbd>C</kbd> twice to exit the program without closing the window (you may also have to press <kbd>Enter</kbd>). After stopping the application press the _Reset_ button on the Photon to prepare it for the next run. 

## Conclusion &amp; Next Steps
In this lab you learned how to write a Node.js/Johnny Five application that collects environment telemetry and sends it to Azure IoT Hub. In the [next lab][2] you will setup some Azure services to store and visualize the data.

[Next Lab ->][2]

{% include next-previous-post-in-category.html %}

[1]: https://store.particle.io/?product=photon-kit
[2]: /azure/02/