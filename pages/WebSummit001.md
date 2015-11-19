---
layout: page-fullwidth
title: "Setting Up Your Device"
subheadline: "Microsoft Azure IoT Lab 1"
teaser: "In this lab you will claim your Photon, provision an Azure IoT Hub and an IoT Hub device."
show_meta: true
comments: true
header: no
breadcrumb: true
categories:
    - iot-photon-labs
    - maker-101
author: "Josh Holmes"
permalink: "/websummit/01/"
---
### Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will claim your Particle Photon (associate it with your Particle account) and create a new Azure IoT device (a software reference to your physical device) that you will use to send telemetry to Azure. 

## Bill of Materials
What you will need:

1. [Particle Photon][1]
2. USB to micro-USB cable (there is one included in the [Photon Development Kit][1])
3. [SparkFun Weather Shield for Particle Photon - $32.95](https://www.sparkfun.com/products/13630)

For this lab series you are using a Particle Photon, a small Wi-Fi enabled development board. The Photon is an excellent prototyping board for connected Things and Particle, the makers of the Photon, sell the P0 chip that drives the board (so when you are ready to go into production you can easily use the same chip). While you can develop for the Photon using Particle Build or Particle Dev and leverage the Particle Cloud services, this lab series is designed to teach you how to build a Wi-Fi based hub-and-spoke system, similar to how SmartThings, Lowes Iris, or Phillips Hue works. For the first few labs you will learn how to create Node.js applications that run on your PC and control the Photon. If you choose to continue with these labs on your own, you will learn how to deploy the Node.js applications to a hub device, like a Raspberry Pi or an Arduino Y&uacute;n, which will act as the field gateway for all of the connected _Things_ in your solution.

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
10. Click on the verify icon in the upper-left toolbar (it looks like a checkmark). In the lower-left corner of the IDE you will see the verification status.
11. After the app is verified, click the flash firmware icon in the upper-left toolbar (it looks like a lightening bolt) to flash your Photon with the VoodooSpark firmware.

<img src="/images/particlebuild01.png"/>

After completing the last step, you should see the indicator LED on your Photon begin to blink. When it returns to the 'breathing' cyan the update is complete.

## Connecting the Weather Shield
The weather shield comes equipped with two onboard sensors - a HTU21D humidity sensor capable of gathering both humidity and temperature, and a MPL3115A2 barometric pressure sensor. There are connections for other optional sensors, such as wind, rain and soil moisture sensors (we will not be working with the optional sensors in this lab series). Simply plug the Photon into the weather shield so that the USB port on the Photon is on the same side as the RJ-11 plugs on the weather shield. 

<img src="/images/Photon_Weather_Shield.jpg"/>


## Create a New Azure IoT Device

At this point your device is provioned with Particle but we still need an identity with Azure so that we can authenticate and send data to our IoT Hub. 

There is a utiltity called _DeviceExplorer_ installed on your lab computer and a link on your desktop. Open the _DeviceExplorer_ utiltiy, open the _Management_ tab and click the _Create_ button. In the dialog that opens, enter the name of your device (use the Particle Photon device ID or the alias you named your device). Then Click the _Create_ button, and click _Done_ on the confirmation dialog that opens.

<img src="/images/deviceexplorer02.png"/> 

You will see your device in the _Devices_ list. Once a device is created, you can get the device-specific connection string by selecting it in the _Devices_ list, right-clicking and selecting _Copy connection string for selected device_:

<img src="/images/deviceexplorer03.png"/> 

The device-specific connection string identifies the device by name and includes a key that is only for that device. Copy the device connection string somewhere that you will be able to access it shortly.

## Conclusion &amp; Next Steps

Congratulations! You have created a physical device and a software representation of that device. In the [next lab][2] you will build a Node.js application that will collect data from the Photon and the weather shield and send the data to Azure IoT Hub.

[Next Lab ->][2]

{% include next-previous-post-in-category.html %}

[1]: https://store.particle.io/?product=photon-kit
[2]: /websummit/02/
[deviceexplorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md