---
layout: "page-fullwidth"
title: "Setting Up Your Raspberry Pi 2"
subheadline: "Building Connected Things with Windows 10 IoT and Microsoft Azure"
teaser: "In this lab, you will install Windows 10 IoT Core on your Raspberry Pi and configure it for use in labs, tutorials, and workshops."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [raspberry-pi, windows-10, grove, c#, iot, maker]
permalink: /getting-started/win10iot/Raspberry Pi 2/
---
# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab, you will install Windows 10 IoT Core on your Raspberry Pi 2. 

# Download and Install the Windows 10 IoT Core Dashboard
The Windows 10 IoT Core Dashboard is a utility for flashing, configuring, and monitoring your Windows IoT Core devices. 

1. Using a browser, navigate to the [Microsoft Windows IoT Downloads &amp; Tools page](http://ms-iot.github.io/content/en-US/Downloads.htm){:target="_blank"}. 
2. Click on the __Get IoT Core Dashboard__ button to download the dashboard utility.
3. Once the __setup.exe__ has downloaded, open it to install the dashboard utility.

# Install Windows 10 IoT Core on Raspberry Pi 2
Windows 10 IoT Core is a version of Windows 10 designed to run on small devices, like the Raspberry Pi 2 (Raspberry Pi 2). You can download and install the Windows IoT Core image onto the microSD card. 

## Flash a microSD Card with the Current Windows 10 IoT Core
The Windows IoT Core Dashboard makes it easy to download and flash a microSD card with the latest publicly available Windows 10 IoT Core image. If you want to install a different version of Windows 10 IoT Core (an older version, or a Windows Insider build, follow the instructions [here]({{ site.url }}/getting-started/windows-10-rpi-custom/), otherwise follow these instructions:

1. Insert the microSD card into your SD card writer.
2. In the Windows IoT Dashboard, select _Set up a new device_.

   ![Set up a new device]({{ site.urlimg }}/getting-started/rpi/dashboard-setup01.png)

3. Use the drop-down list to make _Device Type_ = __Raspberry Pi 2__.
4. Select __Windows 10 IoT Core for Raspberry Pi 2__.
5. Select the microSD card in the _Drive_ drop-down list.
6. Check the box for _I accept the software license terms_ (of course, you have already read them).
7. Click the __Download and install__ button.

    ![Download and install]({{ site.urlimg }}/getting-started/rpi/dashboard-setup02.png)

11. When prompted, select __Continue__ indicating that you have backed up any files from the microSD card before it gets erased.

    ![Accept License]({{ site.urlimg }}/getting-started/rpi/dashboard-setup03.png)

    The download and install could take some time depending on your bandwidth and the size of the microSD card. When it is complete you will see the _Your SD card is ready_ screen.

    ![Your SD card is ready]({{ site.urlimg }}/getting-started/rpi/dashboard-setup04.png)

## Connect and Run First Boot of the Raspberry Pi 2
You are now ready to connect and power on your Raspberry Pi 2.

1. Insert the microSD card with the Windows 10 IoT Core image on it into your Raspberry Pi 2 (the slot is on the underside, on the opposite edge from the side with the USB ports).
2. If you will be connecting to a physical network, connect a network cable from your local network to the Ethernet port on the Raspberry Pi 2. Your development device must be on the same network. If you will be using Wi-Fi, see the _Joining the Wi-Fi Network_ section below.
3. If you will be working with a monitor, connect an HDMI monitor to the HDMI port on the board.
4. Connect the power supply to the micro USB port on the board. You must power this from the 5V 2A adapter - USB power from your computer is insufficient.

Windows 10 IoT Core will boot on power-up. The first boot may take a few minutes. If you have the Raspberry Pi 2 connected to a display, you should see the default app screen.

![Default app]({{ site.urlimg }}/getting-started/rpi/defaultapp.png)

# Conclusion &amp; Next Steps
In this lab, you flashed Windows 10 IoT Core onto your Raspberry Pi 2 for labs, tutorials, and workshops on __ThingLabs.io__ that use Windows 10 IoT. 

Next you need to configure your Raspberry Pi 2 for use in the labs, including getting it on a network, giving it a unique machine name, and changing the Administrator password.

<a class="radius button small" href="{{ site.url }}/getting-started/win10iot/rpi-config/">Go to 'Configure Your Raspberry Pi' ›</a>