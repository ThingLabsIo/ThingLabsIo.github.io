---
layout: page-fullwidth
title: "Setting Up Your Arduino"
subheadline: "Node.js - Connected Weather Station"
teaser: "In this lab you will install firmware onto your Arduino that will enable communication with the gateway application."
show_meta: true
comments: true
header: no
breadcrumb: true
categories: [arduino, photon, iot, maker, javascript, node.js, johnny-five]
permalink: /workshop/js/weather/setup-arduino/
---
### Table of Contents
*  Auto generated table of contents
{:toc}

If you haven't already done so, please follow the instructions in ['Getting Started'](./getting-started/) section.

In this lab you will install the Standard Firmatta onto your Arduino (or SparkFun RedBoard...which we include when we refer to the Arduino Uno). The Standard Firmatta exposes basic input/output on the device for your gateway application to access.

# Install the Arduino IDE
While you won't use the Arduino IDE very much in the set of labs, it is necessary for a couple of things. For one thing, installing the Arduino IDE also installs the USB drivers for the Arduino board.

Go to [http://www.arduino.cc][arduino]{:target="_blank"} and follow the links to download the latest version of the Arduino IDE. Make sure that the checkbox for the USB driver is selected during install (it typically is by default).

# Upload the Standard Firmata to your Arduino
For this lab series you will use tools that require your Arduino to have the Standard Firmatta loaded. Fortunately, this is easy to do using the Arduino IDE.

1. Connect your Arduino board via USB to your computer.
2. Open the Arduino IDE.
3. Using the _Tools_ menu, select _Board_ and make sure the type of board you are using (Arduino Uno or Arduino Y&uacute;n) is selected.
4. Using the _Tools_ menu, select _Port_ and make sure your board is selected on a COM port.
5. Using the _File_ menu, select _Examples_ > _Firmata_ > _StandardFirmata_. This will open a new Arduino IDE window with the code for the Standard Firmata loaded.
6. Using the _Sketch_ menu, select _Upload_. This will upload the firmware to your Arduino (you should see the lights on the Arduino blink while this happens).
7. Close all Arduino IDE windows.

# Connecting the Weather Shield
The weather shield comes equipped with two onboard sensors - a HTU21D humidity sensor capable of gathering both humidity and temperature, and a MPL3115A2 barometric pressure sensor. There are connections for other optional sensors, such as wind, rain and soil moisture sensors (we will not be working with the optional sensors in this lab series). Simply plug the Weather Shield into the Arduino (the pins only match one way).

![Arduino Weather Shield](/images/devices/arduino-weather-station.jpg)

# Conclusion &amp; Next Steps
You have configured your prototyping device for use in this workshop. Now you can proceed to the next lab.

<a class="radius button small" href="../hello-iot-world/">Go to  'Hello, IoT World!' ›</a>

[uno]: http://www.arduino.cc/en/Main/ArduinoBoardUno
[yun]: http://www.arduino.cc/en/Main/ArduinoBoardYun
[arduino]: http://www.arduino.cc