---
layout: "page-fullwidth"
title: "Setting Up Your Raspberry Pi 2"
subheadline: "Building Connected Things with Windows 10 IoT and Microsoft Azure"
teaser: "In this lab you will install Windows 10 IoT Core on your Raspberry Pi 2."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [raspberry-pi, windows-10, grove, c#, iot, maker]
permalink: /workshop/windows-thingy/setup-rpi2/
---
# Table of Contents
*  Auto generated table of contents
{:toc}

If you haven't already done so, please follow the instructions in the ['Getting Started' lab](../getting-started/) section.

In this lab you will install Windows 10 IoT Core on your Raspberry Pi 2 (RPi2). 

# Install Windows 10 IoT Core on Raspberry Pi 2
Windows 10 IoT Core is a version of Windows 10 designed to run on small devices, like the Raspberry Pi 2 (RPi2). You can download and install the Windows IoT Core image onto the micro SD card. 

1. In a browser, navigate to the [Microsoft Windows IoT Downloads &amp; Tools page](http://ms-iot.github.io/content/en-US/Downloads.htm){:target="_blank"}. 
2. Click on the __Get IoT Core Dashboard__ button to download the dashboard utility.
3. Once the __setup.exe__ has downloaded, open it to install the dashboard utility.

   Once the Windows IoT Core Dashboard is installed, use it to flash the microSD card with the latest Windows IoT Core OS image.

4. Insert the micro SD card into your SD card writer.
5. In the IoT Dashboard, select _Set up a new device_.

   ![Set up a new device](/images/rpi2/dashboard-setup01.png)

6. Use the dropdown list to make _Device Type_ = __Raspberry Pi 2__.
7. Select __Windows 10 IoT Core for Raspberry Pi 2__.
8. Select the microSD card in the _Drive_ dropdown list.
9. Check the box for _I accept the software license terms_ (of course you have already read them).
10. Click the __Download and install__ button.

    ![Download and install](/images/rpi2/dashboard-setup02.png)

11. When prompted, select __Continue__ indicating that you have backed up any files from the microSD card before it gets erased.

    ![Accept License](/images/rpi2/dashboard-setup03.png)

    The download and install could take some time depending on your bandwidth and the size of the microSD card. Whe it is complete you will see the _You SD card is ready_ screen.

    ![Your SD card is ready](/images/rpi2/dashboard-setup04.png)

# Connect the RPi2
You are now ready to connect and power on your RPi2.

1. Insert the microSD card with the Windows 10 IoT Core image on it into your RPi2 (the slot is on the underside, on the opposite edge from the side with the USB ports).
2. If you will be connecting to a physical network, connect a network cable from your local network to the Ethernet port on the RPi2. Your development device must be on the same network. If you will be using Wi-Fi, see the Joinning the Wi-Fi Network section below.
3. If you will be working with a monitor, connect an HDMI monitor to the HDMI port on the board.
4. Connect the power supply to the micro USB port on the board. You must power this from the 5V 2A adapter - USB power from your computer is insufficient.

Windows 10 IoT Core will boot on power-up. The first boot may take a few minutes. If you have the RPi2 connected to a display, you should see the default app screen.

![Default app](/images/rpi2/rpi2_defaultapp.png)

# Join the Wireless Network
If your board has a WiFi adapater and it has not yet be set up, it will start to broadcast itself as a network. Unconfigured boards will begin with "AJ_" (e.g. AJ_58EA6C68).

![Devices broadcasting their ID](/images/rpi2/dashboard-setup05.png)

1. Go to __My devices__. From my devices, you can dsicover your device and configure it (including connecting to Wi-Fi).
2. Find your board and click __Configure Device__. If you don’t see your board, make sure that you’ve allowed enough time for your board to boot. If all else fails, reboot your device.
3. Enter your network credentials. Your computer will now connect to your board.

# Set the Machine Name and Password
By default your board will have the name __minwinpc__ and an Administrator password of __p@ssw0rd__. It is recommend that you change both of these to avoid confusion with other devices on your network.

1. Find your device in the list
2. Click the edit symbol (pencil symbol). This will take you to settings page. From the settings page you can launch Windows Device Portal, and set basic settings.
3. Set the machine name and change the password (highly recommended). All devices start with a default password. The default password is __p@ssw0rd__. It is highly suggested that you change it.
4. Click __Accept__ to accept the changes and reboot the RPi2

Your RPi2 will reboot and when it is back up and running it will have the new name you gave it, and the Administrator account will use the password you created. 

# Test Device
Quick-Run samples are pre-built and require no compiling or coding to get going. This is a great way to make sure everything is working and easily play with your board.

1. Navigate to __Try some samples__ in the left nav bar.
2. Select _Hello Blinky_
3. Select your board from the dropdown list
4. Launch the sample. 

In the background, the dashboard will temporarily install the quick-run sample onto you device. Once loaded, the device will broadcast a webpage over the network and IoT Dashboard will automatically connect to it. This lets you control the app without having to plug in a monitor directly to your device.

# Conclusion &amp; Next Steps
In this lab you prepared your Raspberry Pi 2 for the following labs. Next you will do the IoT equivalent of 'Hello, World!' - you will make an LED blink.

<a class="radius button small" href="{{ site.url }}/workshop/windows-thingy/hello-windows-iot/">Go to 'Hello, Windows IoT!' ›</a>