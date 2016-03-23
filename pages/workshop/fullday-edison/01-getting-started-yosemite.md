---
layout: "page-fullwidth"
title: "Flash the Edison using Mac OS X Yosemite"
subheadline: "Building Connected Things with Node.js and Microsoft Azure"
teaser: "This is a step-by-step guide to flashing the Intel Edison board using Mac OS Yosemite."
show_meta: true
comments: false
header: no
breadcrumb: true
categories: [intel-edison, node, iot, maker]
permalink: /workshop/fullday-edison/getting-started/yosemite
---

The following is based on the [Instructions from Intel](http://www.intel.com/content/www/us/en/support/boards-and-kits/000005801.html).

## Flash the Edison with the Latest Yocto Image: Mac OS X Yosemite
 
Ensure the Intel Edison is attached to the Arduino expansion board.

Install Homebrew if it is not already installed:

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Install dfu-util, coreutils, and gnu-getopt if not already installed:

```
brew install dfu-util coreutils gnu-getopt
```
    
Download and extract the <a target="_blank" href="https://software.intel.com/en-us/iot/hardware/edison/downloads">Release #.# Yocto complete image</a> and Extract the contents of to your development directory (e.g. ~/Development/).

Using Terminal (Command + Space and type Terminal), change to the directory where you extracted the Yocto image (your directoyr name and the name/version of the Edison folder may be different).

```
cd ~/Development/edison-iotdk-image-280915
```
    
Run the flashall script and then plug the USB cables into board.

```
./flashall.sh
```
    
<blockquote>Note: The script can take up to 5 minutes to complete the flashing and another 2 minutes to reboot.</blockquote>

You should now have the latest image on your Intel® Edison development board.

[Return to Getting Started](../getting-started/)