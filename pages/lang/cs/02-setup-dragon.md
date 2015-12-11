---
layout: "page-cs"
title: "Setting Up Your DragonBoard 410c"
subheadline: "Building Connected Things with Windows 10 IoT and C#"
teaser: "In this lab you will install Windows 10 IoT Core on your DragonBoard 410c."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [dragonboard, windows-10, c#, iot, maker]
permalink: /lang/cs/setup-dragon/
---
# Table of Contents
*  Auto generated table of contents
{:toc}

If you haven't already done so, please follow the instructions in the ['Getting Started' lab](../getting-started/) section.

In this lab you will install Windows 10 IoT Core on your DragonBoard 410c. 

# Bill of Materials
What you will need:

1. [DragonBoard 410c - $75.00](http://partners.arrow.com/campaigns-na/qualcomm/dragonboard-410c/)
2. [WM24P-12-A-QL 12V 2A Switching Power Supply - $11.24](https://www.arrow.com/en/products/wm24p-12-a-ql/autec-power-systems#page-1)
6. 8GB micro SD card - class 10 or better. Microsoft suggests [this one](http://www.amazon.com/gp/product/B00IVPU786) or [this one](http://www.amazon.com/SanDisk-Ultra-Micro-SDHC-16GB/dp/9966573445).
7. Micro SD card writer

# Install the DragonBoard Update Tool
Download the DragonBoard Update Tool by selecting the download link appropriate for your PC: [x86](https://developer.qualcomm.com/download/db410c/windows-10-iot-update-tool-dragonboard-410c-x86.zip) or [x64](https://developer.qualcomm.com/download/db410c/windows-10-iot-update-tool-dragonboard-410c-x64.zip). Extract the contents of the ZIP file. Open the folder and double-click the setup.exe file.

Follow the instructions and when prompted click __Install__ to install the USB drivers for the DragonBoard.

# Download Windows 10 IoT Core
Windows 10 IoT Core is a version of Windows 10 designed to run on small devices, like the DragonBoard 410c. You can download and install the Windows IoT Core image on your DragonBoard. 

1. Download a Windows 10 IoT Core image from the [Microsoft downloads page](http://ms-iot.github.io/content/en-US/Downloads.htm). 
2. Save the ISO to a local folder (e.g. C:\Development\IoTLabs).
3. Double-click the ISO to mount it as a virtual drive. 
4. Run __Windows_10_IoT_Core_QCDB410C.msi__. This will install the _Windows IoT Core Watcher_ utility and the QCDB410C FFU (Field Firmware Update).
5. Eject the virtual drive after the installation is complete.

Now that the utilities and firmware (FFU) are on your development device, you can install the Windows IoT Core image on the DragonBoard.

# Connect the DragonBoard to the Host PC
You must enable _download mode_ by setting the first boot switch on the back of the board to: __USB Boot__. The remaining three (3) switches should be in the __OFF__ position.

<img src="/images/dragon/dragon-usbboot.png"/>

Connect the USB cable from the Host PC to the DragonBoard.

Connect the DragonBoard to the power supply.

# Flash the Windows 10 IoT Core Image to the DragonBoard

Start the _DragonBoard Update Tool_ (when it installed it created a shortcut in you applications list under _Qualcomm_ > _DragonBoardUpdate Tool_).

The Update Tool should detect the DragonBoard and the _Connection Status_ should show: __Connected__.

In the _DragonBoard Update Tool_ click the __Browse__ button and locate the _C:\Program Files (x86)\Microsoft IoT\FFU\QCDB410C\flash.ffu_ Windows 10 IoT Core image file you downloaded previously.

Click __Program__ to flash the DragonBoard with the Windows 10 IoT Core image.

<blockquote>
	Flashing the DragonBoard will overwrite any previous content of the memory.
</blockquote>

Once the flash is complete, disconnect the power supply from the board and change all of the boot switches back to the _Off_ position (switchs 2-4 should already be in the _Off_ position).

The DragonBoard is now ready to boot into Windows 10 IoT Core.

# Power On the DragonBoard

1. Connect a USB mouse and keyboard.
2. Connect an HDMI monitor to the HDMI port on the board.
3. Connect the power supply. You must power this from the 12V 2A adapter - USB power from your computer is insufficient.

After a few seconds you should see the Windows 10 IoT boot-logo and shortly after you should see the Windows 10 IoT Core default application.

<img src="/images/dragon/dragon_defaultapp.jpg"/>

# Connecting to a Wi-Fi Network
The DragonBoard 410c has onboard Wi-Fi to connect to a local network (you may use a USB Ethernet Adapter instead if Wi-Fi is not available or not preferred). For this lab we will assume a Wi-Fi network is available.

1. Click the Settings button next to the clock on the Win10 IoT Core default application.
2. On the Settings page, select "Network & Wi-Fi" via the menu on the left.
3. Your DragonBoard will automatically begin scanning for wireless networks.
4. Once your network appears in the list, select it and click the "Connect" button to connect.

## Set the Machine Name and Password
By default the DragonBoard will have a machine name of _minwinpc_ and an Administrator password of _p@ssw0rd_. It is recommend that you change both of these to avoid confusion with other devices on your network.

Launch PowerShell using the _Run as Administrator_ option (right-click on the PowerShell icon to reveal this option). Run the following set of commands:

You may need to start the WinRM service on your desktop to enable remote connections:

{% highlight PowerShell %}
net start WinRM
{% endhighlight %}

With the WInRM service running, add the DragonBoard to your Trusted Hosts. Replace _\<machine-name or IP Address\>_ with your actual machine name or IP address (this will be displayed on the default app screen when your DragonBoard is running).

{% highlight PowerShell %}
Set-Item WSMan:\localhost\Client\TrustedHosts -Value <machine-name or IP Address>
{% endhighlight %}

Enter __Y__ to confirm the change.

Start a PowerShell session with your DragonBoard. 

{% highlight PowerShell %}
Enter-PSSession -ComputerName <machine-name or IP Address> -Credential <machine-name or IP Address or localhost>\Administrator
{% endhighlight %}

When prompted, enter the Administrator password (_p@ssw0rd_ is the default). If you successfully connected to the device, you should see the IP address of your device before the prompt.

Next, change the Administrator password (replace _\<new password\>_ with a strong password):

{% highlight PowerShell %}
net user Administrator <new password>
{% endhighlight %}

Optionally you can change the name of your DragonBoard (replace _<new name>_ with the name you prefer):
 
{% highlight PowerShell %}
setcomputername <new name>
{% endhighlight %}

Finally, reboot the DragonBoard.
 
{% highlight PowerShell %}
shutdown /r /t 0
{% endhighlight %}

Your DragonBoard will reboot and when it is back up and running it will have the new name you gave it, and the Administrator account will use the password you created. 

# Conclusion &amp; Next Steps
In this lab you prepared your DragonBoard 410c for the following labs. Next you will do the IoT equivalent 'Hello, World!' - you will make an LED blink.

<a class="radius button small" href="{{ site.url }}/lang/cs/hello-windows-iot/">Go to 'Hello, Windows IoT!' ›</a>