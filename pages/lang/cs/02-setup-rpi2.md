---
layout: "page-cs"
title: "Setting Up Your Raspberry Pi 2"
subheadline: "Building Connected Things with Windows 10 IoT and C#"
teaser: "In this lab you will install Windows 10 IoT Core on your Raspberry Pi 2."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [raspberry-pi, windows-10, c#, iot, maker]
permalink: /lang/cs/setup-rpi2/
---
# Table of Contents
*  Auto generated table of contents
{:toc}

If you haven't already done so, please follow the instructions in the ['Getting Started' lab](../getting-started/) section.

In this lab you will install Windows 10 IoT Core on your Raspberry Pi 2. 

# Bill of Materials
What you will need:

1. [Raspberry Pi 2 - $42.00](http://www.amazon.com/Raspberry-Pi-Model-Project-Board/dp/B00T2U7R7I/)
2. [5V 2A Switching Power Supply w/ 20AWG 6' MicroUSB Cable - $7.95](https://www.adafruit.com/product/1995)
6. 16GB micro SD card - class 10 or better. Microsoft suggests [this one](http://www.amazon.com/gp/product/B00IVPU786) or [this one](http://www.amazon.com/SanDisk-Ultra-Micro-SDHC-16GB/dp/9966573445).
7. Micro SD card writer

# Install Windows 10 IoT Core on RaspBerry Pi 2
Windows 10 IoT Core is a version of Windows 10 designed to run on small devices, like the Raspberry Pi 2 (RPi2). You can download and install the Windows IoT Core image onto the micro SD card. 

1. Download a Windows 10 IoT Core image from the [Microsoft downloads page](http://ms-iot.github.io/content/en-US/Downloads.htm). 
2. Save the ISO to a local folder (e.g. C:\Development\IoTLabs).
3. Double-click the ISO to mount it as a virtual drive. 
4. Install __Windows_10_IoT_Core_RPi2.msi__. This will install the _Windows IoT Core Watcher_ utility and the RPi2 FFU (Field Firmware Update).
5. Eject the virtual drive after the installation is complete.

Now that the utilities and firmware (FFU) are on your development device, you can create a Windows IoT Core image on the SD card.

1. Insert the micro SD card into your SD card writer.
2. Click on the Windows icon (Start menu) and type _WindowsIoT_ - select the __WindowsIoTImageHelper__ from the _Best match_ list.
3. When the tool launches, select the SD card from the list.
4. Select the FFU file be browsing to __C:\Program Files (x86)\Microsoft IoT\FFU\RaspberryPi2__
5. Once the SD card is complete, safely eject the SD card (use either _Safely Remove Hardware_ from the taskbar, or right-click on the SD card in File Explorer and choose _Eject_ - failing to do so may corrupt the SD card.). 

# Connect the RPi2
You are now ready to connect and power on your RPi2.

1. Insert the micro SD card with the Windows 10 IoT Core image on it into your RPi2 (the slot is on the underside, on the opposite edge from the side with the USB ports).
2. Connect a network cable from your local network to the Ethernet port on the RPi2. Your development device must be on the same network.
3. Connect an HDMI monitor to the HDMI port on the board.
4. Connect the power supply to the micro USB port on the board. You must power this from the 5V 2A adapter - USB power from your computer is insufficient.

Windows 10 IoT Core will boot on power-up. The first boot may take a few minutes.

<img src="/images/rpi2/rpi2_defaultapp.png"/>

# Set the Machine Name and Password
By default the RPi2 will have a machine name of _minwinpc_ and an Administrator password of _p@ssw0rd_. It is recommend that you change both of these to avoid confusion with other devices on your network.

Launch PowerShell using the _Run as Administrator_ option (right-click on the PowerShell icon to reveal this option). Run the following set of commands:

You may need to start the WinRM service on your desktop to enable remote connections:

{% highlight PowerShell %}
net start WinRM
{% endhighlight %}

With the WInRM service running, add the RPi2 to your Trusted Hosts. Replace _\<machine-name or IP Address\>_ with your actual machine name or IP address (this will be displayed on the default app screen when your RPi2 is running).

{% highlight PowerShell %}
Set-Item WSMan:\localhost\Client\TrustedHosts -Value <machine-name or IP Address>
{% endhighlight %}

Enter __Y__ to confirm the change.

Start a PowerShell session with your RPi2. 

{% highlight PowerShell %}
Enter-PSSession -ComputerName <machine-name or IP Address> -Credential <machine-name or IP Address or localhost>\Administrator
{% endhighlight %}

When prompted, enter the Administrator password (_p@ssw0rd_ is the default). If you successfully connected to the device, you should see the IP address of your device before the prompt.

Next, change the Administrator password (replace _\<new password\>_ with a strong password):

{% highlight PowerShell %}
net user Administrator <new password>
{% endhighlight %}

Optionally you can change the name of your RPi2 (replace _<new name>_ with the name you prefer):
 
{% highlight PowerShell %}
setcomputername <new name>
{% endhighlight %}

Finally, reboot the RPi2.
 
{% highlight PowerShell %}
shutdown /r /t 0
{% endhighlight %}

Your RPi2 will reboot and when it is back up and running it will have the new name you gave it, and the Administrator account will use the password you created. 

# Conclusion &amp; Next Steps
In this lab you prepared your Raspberry Pi 2 for the following labs. Next you will do the IoT equivalent 'Hello, World!' - you will make an LED blink.

<a class="radius button small" href="{{ site.url }}/lang/cs/hello-windows-iot/">Go to 'Hello, Windows IoT!' ›</a>