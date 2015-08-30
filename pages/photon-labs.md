---
layout: page-fullwidth
title: "Particle Photon IoT Labs"
subheadline: "Building Connected Things"
teaser: "In these IoT Labs you will begin by learning the basics of working with micro-controllers and sensors, and move on to connecting them to the Internet. You will learn how to leverage Cloud IoT services to collect data and control devices and use advanced services like machine learning and analytics to discover insights using your Things."
show_meta: true
comments: true
header: no
breadcrumb: true
categories:
    - iot-photon-labs
    - maker-101
    - connected-things-101
permalink: "/photon/"
---

### Table of Contents
*  Auto generated table of contents
{:toc}

Many connected solutions today use a common pattern: the hub and spoke pattern. This pattern defines a central hub device that is connected to one or more cloud services, and one or more spoke devices that are connected to the hub. The hub actsa as a broker to and a coordinator for the spoke devices. The hub is responsible for collecting telemetry from the spoke devices and sending it to the cloud, and for receiving commands from the cloud and brokering them to the correct devices. Popular products like SmartThings, Wink, Phillips Hue and others use this pattern. Each of these solutions has a hub device that connects to one or more spoke devices.

In this lab series you will learn how to build your own hub and spoke home automation solution that is connected to Microsoft Azure. This particular lab series will use the Particle Photo as the spoke device form factor. The Photon is a small, Wi-Fi enables maker board that costs only $19. For the hub device you will use a Raspberry Pi 2. The code you write will be in JavaScript using Node.js. One of the advantages of JavaScript and Node.js is that the code you write is very portable. In fact, the code you write can be used for many devices, not just the Raspberry Pi 2 and the Photon, but a variety of Arduino boards as well, and others. 

This lab series will start by ensuring you understand the basics - building small devices that both send output (turn things on and off) and collect input (read data in). THat will be followed by learning the basics for publishing telemetry and subscribing to message streams, which enables you to collect data in the cloud and send commands back to your devices. You will learn how to use cloud services to learn about the data you are collecting and to gain insight from it. Finally you will put all of these capabilities together to build a home automation solution of your own.
 
## [Getting Started][1]
This is a step-by-step guide to preparing your computer for the IoT Labs.

Estimated time to complete: 30-minutes

## Maker 101

### [Writing Digital Output][2]
In this lab you will write to a digital output.

Estimated time to complete: 20-minutes

### [Reading Analog Input][3]
In this lab you will read input from an analog sensor using a voltage divider.

Estimated time to complete: 20-minutes

### [Input Controls Output][4]
In this lab you will use analog input to control digital output.

Estimated time to complete: 20-minutes

## Connected Things 101

### [Sending Data to the Cloud][5]
In this lab you will build a simple ambient light detection app and send the data that is being collected to the Cloud.

Estimated time to complete: 30-minutes

### [Command &amp; Control][6]
In this lab you will build a circuit that acts as both a message sending device (an ambient light detector) and a command/control receiving device (LED). You will learn how to send command/control messages and how to process them. These are the concepts of connected Things.

Estimated time to complete: 40-minutes

### [Connecting Multiple Things][7]
In this lab you will build two Things, and enable one Thing to share its state with the other, which will alter its own state based on the information received. In other words, you will build one connected Thing that controls another Connected Thing.

### Coming Soon
More labs will be comming soon. Check back regularly.

[1]: /photon/00/
[2]: /photon/01/
[3]: /photon/02/
[4]: /photon/03/
[5]: /photon/04/
[6]: /photon/05/
[7]: /photon/06/