---
layout: "page-fullwidth"
title: "The GrovePi"
subheadline: "Building Connected Things with Windows 10 IoT and Microsoft Azure"
teaser: "This section describes the GrovePi shield and how it interfaces with the Raspberry Pi 2."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [raspberry-pi, windows-10, dragonboard, minnowboard, c#, iot, maker]
permalink: /workshop/thingy-4-windows/grovepi/
---

# Shield and Port Descriptions
The GrovePi is stacked on top of the Raspberry Pi 2 without the need for any other connections.  Communication between the two occurs over the I2C interface. All Grove modules connect to the universal Grove connectors on the GrovePi shield via the universal 4 pin connector cable.

Grove modules, which work on analog and digital signals, connect directly to the ATMEGA328 microcontroller (the same MCU as the Arduino Uno) on the Grove Pi.  The microcontroller acts as an interpreter between the Raspberry Pi 2 and the Grove sensors.  It sends, receives, and executes commands sent by the Raspberry Pi 2.

In addition, the GrovePi allows the Raspberry Pi 2 to access some Grove sensors directly.  The Raspberry Pi 2 has an I2C Bus and a Serial bus.  These buses can directly connect to sensors via the I2C Ports and the USART Port.

![GrovePi Port Descriptions](/images/workshops/thingy-4-windows/GrovePi-Port-description.jpg)

The GrovePi runs an ATmega328 which contains an onboard 6 channel analog-to-digital (A/D) converter. The A/D converter has 10-bit resolution, returning values __0-1023__. Analog pins are usually used for reading analog sensors but can also be used for general purpose i/o, just like the digital pins 0-13.
If you need more digital sockets, you can re-purpose an analog socket.

The __pinMode()__ method is used to set the pin to __INPUT__ or __OUTPUT__.

__analogRead(2)__ and __digitalRead(2)__ will read from different GrovePi sockets.

* _grovepi.analogRead(2)_ will read from the socket labelled __A2__.
* _grovepi.digitalRead(2)_ will read from the socket labelled __D2__.
 
GrovePi sockets __A0__, __A1__, and __A2__ use the A/D converter and support `analogRead()` values __0-1023__.

GrovePi sockets __D2__ through __D8__ are digital and support 1-bit input/output, values __0-1__, using `digitalRead()` and `digitalWrite()`.

GrovePi sockets __D3__, __D5__, __D6__ also support _Pulse Width Modulation (PWM)_ which means you can write 8-bit values __0-255__ with `analogWrite()`.

You can’t use `analogRead()` with D3, D5, D6; it is only supported on A0, A1, and A2 (aka D14, D15, and D16).

# Sensors and Actuators
The sensors and actuators included in the GrovePi+ Starter Kit can be classified as follows:

|Type       |Direction	    |Examples	                                                             |GrovePi API Calls                         |
|-----------|---------------|------------------------------------------------------------------------|------------------------------------------|
|Analog     |Input	        |Light sensor, Temperature sensor, Rotary Angle sensor, Sound sensor     |SensorValue()                             |
|Digital    |Input	        |Button, Ultrasonic Range sensor                                         |CurrentState(), MeasureInCentimeters()    |
|Digital    |Output	        |LED, Buzzer, Relay            	                                         |ChangeState(SensorStatus)                 |
|I2C        |Output         |RGB LCD Display                                                         |SetBackLightRgb(r,g,b), SetText(string)   |

This information is taken from <http://www.dexterindustries.com/GrovePi/engineering/port-description/>.

# Conclusion &amp; Next Steps
In this lab, you learned how the GrovePi solution works. Next, you will use the Raspberry Pi 2 and the GrovePi to build the IoT equivalent of 'Hello, World!'. That is to say, you will make an LED blink.

<a class="radius button small" href="{{ site.url }}/workshop/thingy-4-windows/hello-windows-iot/">Go to 'Lab 01: Hello, Windows IoT!' ›</a>