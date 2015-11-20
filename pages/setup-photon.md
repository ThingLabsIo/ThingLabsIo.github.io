---
layout: page-fullwidth
title: "Setting Up the Particle Photon"
subheadline: "Microsoft Azure IoT Lab 2"
teaser: "In this lab you will claim your Photon and prepare it for use in the lab series."
show_meta: true
comments: true
header: no
breadcrumb: true
categories:
    - photon
    - iot-labs
    - maker-101
author: doug_seven
permalink: "/setup-photon"
---

### Table of Contents
*  Auto generated table of contents
{:toc}

For this lab series you are using a Particle Photon, a small Wi-Fi enabled development board. The Photon is an excellent prototyping board for connected Things and Particle, the makers of the Photon, sell the P0 chip that drives the board (so when you are ready to go into production you can easily use the same chip). While you can develop for the Photon using Particle Build or Particle Dev and leverage the Particle Cloud services, this lab series is designed to teach you how to build an IoT solution using the _gsateway_ patterm, similar to how consumer products like SmartThings, Lowes Iris, or Phillips Hue works using a Node.js application on the gateway that communicates with the Photon over Wi-Fi. For the first few labs you will learn how to create the Node.js applications that run on your PC and controls the baord through a USB connection. If you choose to continue with these labs on your own, you will learn how to deploy the Node.js applications to a gateway device capable of running the Node.js application, such as a Raspberry Pi or an Arduino Y&uacute;n.

## Bill of Materials
The following hardware is needed to complete these labs with the Particle Photon:

1. [Particle Photon Development Kit - $29.00][1]
2. [SparkFun Weather Shield for Particle Photon - $32.95](https://www.sparkfun.com/products/13630)

## Install Particle-CLI
The [Particle-CLI][particlecli] is a command line interface for working with both the Particle Photon and the Particle Cloud. This tool will be used to 'claim' or provision your Photon, and may provide other useful benefits.

On Windows, open the Node.js command prompt and type the following:
<pre>
  npm install -g particle-cli
</pre>

On Mac OS X open Terminal and type the following:
<pre>
  sudo npm install -g particle-cli
</pre>

## Create a Free Particle Cloud Account
The Particle Photon is pre-configured to connect to the Particle Cloud. In order to 'claim' or provision the device, and to update its firmware you must create a free account with Particle Cloud. Go to [https://build.particle.io/signup][particle] to create a new Particle Cloud account.

## Claim Your Photon
Associating a Particle Photon to your Particle account is known as _claiming_ (you are claiming the Photon so no one else can claim it - it's like high-tech cookie-licking). If you refer to the Particle website there is information about how to [claim the Photon using either an iOS or Android device](https://docs.particle.io/guide/getting-started/start/photon/#step-1-power-on-your-device), or [over USB from Windows or OS X](https://docs.particle.io/guide/getting-started/connect/photon/). 

If you use the Android or iOS application for claiming your device and there are other developers around you doing the same thing, it can get confusing figuring out which Wi-Fi SSID goes with which physical device. The trick is to look at the sticker on the underside of the box. The four digits at the end of the barcode are the same four digits that are appended to the SSID name _Photon-_. Simply match the numbers on your box with the SSID and claim your Photon.

<img src="/images/photonclaim.png"/>

<blockquote>
  As of the writing of this tutorial, there is a bug in the USB claiming process for Windows 10. After following the steps to install the Particle USB driver, use the following steps to claim the Photon to your account if you are on Windows 10, or if you experience difficulties on previous versions of Windows.
</blockquote>

Open a terminal window (Mac OS X) or Node.js command prompt (Windows) and execute the following command to get the device ID of your Photon:

<pre>
  particle identify  
</pre>

If the Photon is connected correctly and working, you should see a result similar to this:
<pre>
  Your device id is 21003f000447343335555555
</pre>

Copy the device ID somewhere you can reference it in a moment. Next, enter the following to connect the Photon to your Wi-Fi network:

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

Use the down arrow key to highlight the SSID for the Wi-Fi network you want to connect to and press <kbd>Enter</kbd>. You should be prompted to identify the security protocol (e.g. WPA2, WPA, WEP, Unsecured). Choose the correct option for your Wi-Fi network and press <kbd>Enter</kbd>, then enter the Wi-Fi network password when prompted.

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

At this point your Photon may blink magenta on and off for a few seconds (or up to a few minutes) as it gets any core firmware updates available for it. When the blinking light turns to a 'breathing' cyan then your Photon is ready for use (the 'breathing' cyan is the ready state indicator).
 
## Upload the VoodooSpark Firmware to Your Photon
For this lab series you will be controlling the Photon from a master/hub device (during development this will be your laptop). The controller application will use Node.js and a framework called Johnny Five. In order for these tools to communicate with the Photon, it needs to have the VoodooSpark firmware installed. The VoodooSpark firmware enables direct TCP communication from the host machine (your laptop running the Node.js app) and the Photon (provided they are on the same Wi-Fi network). Installing the VoodooSpark firmware is fairly easy to do using the Particle Build web-based IDE.

1. In a browser open the [VoodooSpark code from GitHub](https://raw.githubusercontent.com/voodootikigod/voodoospark/master/firmware/voodoospark.cpp). Make sure in the comments at the top of the file it specifies '@version V3.1.0' or greater.
2. With your Photon still powered on, use a second browser tab/window to open [Particle Build](https://build.particle.io/).
3. In Particle Build, click on the code icon in the lower-left toolbar (it looks like opening and closing angle brackets &lt; &gt;).
4. Click on the _Create New App_ button.
5. Name the app "VoodooSpark"
6. Copy all of the code from the VoodooSpark GitHub page to your new app (be sure to replace any code in the new app).
7. Click on the _Devices_ icon in the lower-left toolbar (it looks like a targeting sight).
8. Find your new Photon (if you claimed your Photon using the command line interface, there will likely be a device with no name/alias with the text 'No Title (click to edit)' - that is your new Photon - feel free to give it a friendly name at this time).
9. If you have more than one device, click on the gray star that appears next to the Photon when you hover over it to turn it yellow (doing this selects this device as the target for a firmware flash).
10. Click on the carrot (greater then sign) on the right on the name of your new Photon. Choose 0.4.6 from the "Your are building with firmware:" dropdown box.
11. Click on the verify icon in the upper-left toolbar (it looks like a checkmark). In the lower-left corner of the IDE you will see the verification status.
12. After the app is verified, click the flash firmware icon in the upper-left toolbar (it looks like a lightening bolt) to flash your Photon with the VoodooSpark firmware.

<img src="/images/particlebuild01.png"/>

After completing the last step, you should see the indicator LED on your Photon begin to blink. When it returns to the 'breathing' cyan the update is complete.

## Connecting the Weather Shield
The weather shield comes equipped with two onboard sensors - a HTU21D humidity sensor capable of gathering both humidity and temperature, and a MPL3115A2 barometric pressure sensor. There are connections for other optional sensors, such as wind, rain and soil moisture sensors (we will not be working with the optional sensors in this lab series). Simply plug the Photon into the weather shield so that the USB port on the Photon is on the same side as the RJ-11 plugs on the weather shield. 

<img src="/images/Photon_Weather_Shield.jpg"/>


## Conclusion &amp; Next Steps
That's it for now. You are ready to start the next lab: [Sending Telemetry to the Cloud =>][sending-telemetry].

{% include next-previous-post-in-category.html %}

[getting-started]: /getting-started
[setup-azure-iot-hub]: /setup-azure-iot-hub
[setup-photon]: /setup-photon
[setup-arduino]: /setup-arduino
[sending-telemetry]: /sending-telemetry
[visualize-iot-with-powerbi]: /visualize-iot-with-powerbi
[particle]: https://build.particle.io/signup
[particlecli]: http://www.particle.io/prototype#cli