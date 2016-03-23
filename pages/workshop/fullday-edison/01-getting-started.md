---
layout: "page-fullwidth"
title: "Full Day Intel Edison & Azure IoT Workshop: Getting Started"
subheadline: "Building Connected Things with Node.js and Microsoft Azure"
teaser: "This is a step-by-step guide to preparing your computer for the Intel Edison IoT Labs."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [intel-edison, node, iot, maker]
permalink: /workshop/fullday-edison/getting-started/
---
### Table of Contents
*  Auto generated table of contents
{:toc}

## Preparing for the Intel Edison with Azure IoT Labs
The labs in this series build on each other to enable you to prototype your own Internet of Things (IoT) devices. In this lab you will use Node.js to build an application for that can run on the Intel Edison and many other Node.js-capable devices including the BeagleBone Black/Green, Arduino Y&uacute;n, and others.

## Bill of Materials
In this lab series you will need the following:

1. [Grove Indoor Environment Kit for Intel® Edison](http://www.seeedstudio.com/depot/Grove-Indoor-Environment-Kit-for-Intel-Edison-p-2427.html)
2. [Intel® Edison for Arduino](http://www.seeedstudio.com/depot/Intel-Edison-for-Arduino-p-2149.html)

# Software Requirements

## Visual Studio Code
Install from [here](https://www.visualstudio.com/products/code-vs.aspx)

## PuTTy  (Windows only)
Install [PuTTy](http://www.putty.org/)

# Prereqisites
Follow [Getting Started](https://software.intel.com/en-us/iot/library/edison-getting-started) instructions from Intel to install any needed drivers 

## Flash the Edison to the Latest Yocto Image
 
Attach the Edison chip to the Arduino expansion board

<div id="flash-edison-tabs">
  <ul>
    <li><a href="#windows10"><span>Windows 8/10</span></a></li>
    <li><a href="#windows7"><span>Windows 7</span></a></li>
    <li><a href="#elcapitan"><span>El Capitan</span></a></li>
    <li><a href="#yosemite"><span>Yosemite</span></a></li>
    <li><a href="#linux"><span>Linux</span></a></li>
  </ul>
  <div id="windows10">
    Download and run the <a target="_blank" href="https://software.intel.com/en-us/iot/hardware/edison/downloads">installer from Intel</a>
    
    <p><strong>IMPORTANT:</strong> When running the Windows installer, uncheck everything except for the drivers and the "flash / update" device option.</p>
  </div>
  <div id="windows7">
    Follow the <a target="_blank" href="http://www.intel.com/content/www/us/en/support/boards-and-kits/000005795.html">Instructions from Intel</a>
  </div>
  <div id="elcapitan">
    Follow the <a target="_blank" href="http://rexstjohn.com/intel-edison-el-capitan-setup-process/">Instructions from Rex St. John</a>
  </div>
  <div id="yosemite">
    The following is based on the <a target="_blank" href="http://www.intel.com/content/www/us/en/support/boards-and-kits/000005801.html">Instructions from Intel</a>
    
    1. Install Homebrew if it is not already installed:

    ```
    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    ```

    2. Install dfu-util, coreutils, and gnu-getopt if not already installed:

    ```
    brew install dfu-util coreutils gnu-getopt
    ```
    
    3. Download and extract the <a target="_blank" href="https://software.intel.com/en-us/iot/hardware/edison/downloads">Release #.# Yocto complete image</a> and Extract the contents of to your development directory (e.g. ~/Development/).

    Using Terminal (Command + Space and type Terminal), change to the directory where you extracted the Yocto image (your directoyr name and the name/version of the Edison folder may be different).

    ```
    cd ~/Development/edison-iotdk-image-280915
    ```
    
    Run the flashall script and then plug the USB cables into board.

    ```
    ./flashall.sh
    ```
    
    Note: The script can take up to 5 minutes to complete the flashing.

    You should now have the latest image on your Intel® Edison development board.
  </div>
  <div id="linux">
    Follow the <a target="_blank" href="http://www.intel.com/content/www/us/en/support/boards-and-kits/000005990.html">Instructions from Intel</a>
  </div>
</div>

<script>
$( "#flash-edison-tabs" ).tabs();
</script>
 
## Configure Your Edison
Open terminal, type 'screen /dev/cu.usbs' then hit Tab to autocomplete, add '115200 -L' and hit Enter twice at blank screen
 
<div id="config-edison-tabs">
  <ul>
    <li><a href="#windows10"><span>Windows 8/10</span></a></li>
    <li><a href="#windows7"><span>Windows 7</span></a></li>
    <li><a href="#osx"><span>Mac OS X (all)</span></a></li>
    <li><a href="#linux"><span>Linux</span></a></li>
  </ul>
  <div id="windows10">
    <ol>
    <li>Download PuTTy</li> 
    <li>Select 'serial'</li> 
    <li>Set baud rate to 115200</li>
    <li>Use Device Manager to find your COM port (select the COM port that doesn't say "Edison" anywhere in it)</li>
    <li>Press Enter twice at blank screen</li>
    </ol>
  </div>
  <div id="windows7">
    Follow the <a target="_blank" href="http://www.intel.com/content/www/us/en/support/boards-and-kits/000005795.html">Instructions from Intel</a>
  </div>
  <div id="osx">
    Open terminal, type 'screen /dev/cu.usbs' then hit Tab to autocomplete, add '115200 -L' and hit Enter twice at blank screen
  </div>
  <div id="linux">
    Install 'screen,' use "/dev/ttyUSB0 115200" and hit Enter twice at blank screen
  </div>
</div>

<script>
$( "#config-edison-tabs" ).tabs();
</script>


1. Login with __root__ and no password.
2. Run <code>configure_edison --setup</code>
3. Enter a new password for your Edison
4. Give your Edison a unique name.
5. Enter <code>Y</code> to setup Wi-Fi. If scanning fails to discover your Wi-Fi network, enter <code>0</code> to scan again.
6. Enter the numer for the network you want to connect to.
7. Enter the network password.
8. When the new IP address for your Edison is displayed on screen, make a note of it for later use.

## Install FileZilla
You can either edit code directly on your Intel Edison or you can edit the code on your computer and copy it to your Edison using SCP or FileZilla! FileZilla Client is a fast and reliable cross-platform FTP, FTPS and SFTP client with lots of useful features and an intuitive graphical user interface. 

Install from [filezilla-project.org](https://filezilla-project.org/)

Deploying code you the Edison is as easy as Drag-n-Drop

1. Open FileZilla
2. Enter the IP address of your Edison in the _Host_ field.
3. Enter __root__ in the _Username_ field
4. ENter the password you created in the _Password field_
5. Enter __22__ in the _Port_ field
6. Click _Quickconnect_
7. Drag & drop files from your machine onto the Edison
 

## Installing ubiLinux
Follow the instructions at [ubilinux installation instructions for Intel® Edison](http://www.emutexlabs.com/ubilinux/29-ubilinux/218-ubilinux-installation-instructions-for-intel-edison)
This can take 15-20 minutes.

### Configure WiFi
From a terminal session, <code>vi /etc/network/interfaces</code>

Edit the following, changing <code>Emutex</code> and <code>passphrase</code> to your SSID and passcode respectively.

```
#auto wlan0
iface wlan0 inet dhcp
    # For WPA
    wpa-ssid Emutex
    wpa-psk passphrase
```

Restart the networking service:

```
/etc/init.d/networking restart
```

### Update APT packages
Run <code>apt-get update</code>

### Install Node.js

```
apt-get install curl
curl -sL https://deb.nodesource.com/setup_5.x | bash -

apt-get install --yes nodejs
```

Optional: install build tools

To compile and install native addons from npm you may also need to install build tools

```
apt-get install --yes build-essential
```

### Install NVM

```
git clone https://github.com/creationix/nvm.git ~/.nvm && cd ~/.nvm && git checkout `git describe --abbrev=0 --tags`
```

To activate nvm, you need to source it from your shell:

```
. ~/.nvm/nvm.sh
```

Add these lines to your ~/.bashrc, ~/.profile, or ~/.zshrc file to have it automatically sourced upon login:

```
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh" # This loads nvm
```

### Install Node.js 4.3.1

```
nvm install stable
```