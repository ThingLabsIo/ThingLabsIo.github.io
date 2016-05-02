---
layout: "page-fullwidth"
title: "Setting Up Your Raspberry Pi with a Custom Image"
subheadline: "Building Connected Things with Windows 10 IoT and Microsoft Azure"
teaser: "In this lab, you will install a non-standard version of Windows 10 IoT Core on your Raspberry Pi 2 or 3 and configure it for use in labs, tutorials, and workshops."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [raspberry-pi, windows-10, grove, c#, iot, maker]
permalink: /getting-started/win10iot/rpi-custom/
---
# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab, you will install a non-standard version of Windows 10 IoT Core on your Raspberry Pi 2 or 3. The term _non-standard_ refers to images that are not available in the default dropdown list in the Windows 10 IoT Core Dashboard, including older images and Windows Insider images. If you have a Raspberry Pi 3, you will need to use a Windows Insider image. You need to have a valid Microsoft Account (MSA) and need to be signed up for the _Windows Insider Program_ to be able to download the Insider Preview. 

Learn more about the Windows Insider Program [here](https://insider.windows.com/Home/Index){:target="_blank"}.

# Download and Install the Windows 10 IoT Core Dashboard
The Windows 10 IoT Core Dashboard is a utility for flashing, configuring, and monitoring your Windows IoT Core devices. 

1. Using a browser, navigate to the [Microsoft Windows IoT Downloads &amp; Tools page](http://ms-iot.github.io/content/en-US/Downloads.htm){:target="_blank"}. 
2. Click on the __Get IoT Core Dashboard__ button to download the dashboard utility.
3. Once the __setup.exe__ has downloaded, open it to install the dashboard utility.

# Download the Non-Standard Windows 10 IoT Core Image

1. Using a browser, go to _[Download Windows 10 IoT Core Insider Preview](https://www.microsoft.com/en-us/software-download/windowsiot){:target="_blank"}_.
2. Select the version of Windows 10 IoT Core you want (for Raspberry Pi 3, select the option with the highest version number).
3. Click __Confirm__.
4. When prompted, select __Raspberry Pi__.
5. Click __Confirm__.

   ![Select the Image Version]({{ site.urlimg }}/getting-started/rpi/select-nonstd.png)

6. When prompted, click __Download__ - Note that the download link is only valid for 24-hours.

   ![Select the Image Version]({{ site.urlimg }}/getting-started/rpi/nonstd-download.png)

7. Once the ISO has downloaded (depending on your network, it may take some time), double-click it to mount it as a virtual disk.
8. Double click the Windows Installer Package (.MSI) and follow the installation steps.

# Install Windows 10 IoT Core on Raspberry Pi
Windows 10 IoT Core is a version of Windows 10 designed to run on small devices, like the Raspberry Pi. You can flash the Windows IoT Core image onto a microSD card that the Raspberry Pi will boot from. 

## Flash a microSD Card with the Non-Standard Version of Windows 10 IoT Core
The Windows IoT Core Dashboard makes it easy to flash a microSD card with a non-standard Windows 10 IoT Core image.

1. Launch the Windows 10 IoT Core Dashboard.
2. Insert the microSD card into your SD card writer.
3. Click __Set up a new device__.

   ![Set up a new device]({{ site.urlimg }}/getting-started/rpi/dashboard-setup01.png)

4. Select __Custom__ from the _Device type_ dropdown.
5. Click the __Browse__ button and locate the image you want to install (it will have the _.ffu_ extension).

   The default location for the FFU image file is __C__ > __Program Files (x86)__ > __Microsoft IoT__ > __FFU__ > __RaspberryPi2__ 

6. Select the microSD card in the _Drive_ drop-down list.
7. Check the box for _I accept the software license terms_ (of course, you have already read them).
7. Click the __Download and install__ button.

    ![Download and install]({{ site.urlimg }}/getting-started/rpi/dashboard-setup02.png)

11. When prompted, select __Continue__ indicating that you have backed up any files from the microSD card before it gets erased.

    ![Accept License]({{ site.urlimg }}/getting-started/rpi/dashboard-setup03.png)

    The download and install could take some time depending on your bandwidth and the size of the microSD card. When it is complete you will see the _Your SD card is ready_ screen.

    ![Your SD card is ready]({{ site.urlimg }}/getting-started/rpi/dashboard-setup04.png)

# Connect and Run First Boot of the Raspberry Pi
You are now ready to connect and power on your Raspberry Pi.

1. Insert the microSD card with the Windows 10 IoT Core image on it into your Raspberry Pi (the slot is on the underside, on the opposite edge from the side with the USB ports).
2. If you will be connecting to a physical network, connect a network cable from your local network to the Ethernet port on the Raspberry Pi. Your development device must be on the same network. If you will be using Wi-Fi, see the _Joining the Wi-Fi Network_ section below.
3. If you will be working with a monitor, connect an HDMI monitor to the HDMI port on the board.
4. Connect the power supply to the micro USB port on the board. You must power this from the 5V 2A adapter - USB power from your computer is insufficient.

Windows 10 IoT Core will boot on power-up. The first boot may take a few minutes. If you have the Raspberry Pi connected to a display, you should see the default app screen.

![Default app]({{ site.urlimg }}/getting-started/rpi/defaultapp.png)

# Conclusion &amp; Next Steps
In this lab, you flashed a non-standard version of Windows 10 IoT Core onto your Raspberry Pi for use in labs, tutorials, and workshops on __ThingLabs.io__ that use Windows 10 IoT. 

Next you need to configure your Raspberry Pi for use in the labs, including getting it on a network, giving it a unique machine name, and changing the Administrator password.

<a class="radius button small" href="{{ site.url }}/getting-started/win10iot/rpi-config/">Go to 'Configure Your Raspberry Pi' ›</a>