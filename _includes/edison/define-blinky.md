The application code resides in a JavaScript file in the same directory as the package.json file.

1. Create a file named __blinky.js__ in your lab working directory (e.g. _C:\Development\IoTLabs\MyLab_).

Within the application file (blinky.js), you need to define the abstractions of the devices you will be using within the application. There are four (4) variables that matter:

* A variable to reference the Johnny-Five framework object.
* A variable to reference to the Edison-IO plugin object.
* A variable to represent the physical board.
* A variable to hold the pin number value for the LED.

2. Within the _blinky.js_ file, add the following code:

    {% highlight javascript %}
    'use strict';
    var five = require ("johnny-five"); 
    var Edison = require("edison-io");
    // Pin 13 is the default pin on the large Arduino breakout board.
    var LEDPIN = 13

    // Create a Johnny Five board instance to represent your Arduino.
    // board is simply an abstraction of the physical hardware, whether it is 
    // an Intel Edison, Arduino, Raspberry Pi or other boards. 
    // For boards other than Arduino you must specify the IO plugin.
    var board = new five.Board({
    io: new Edison()
    });
    {% endhighlight %}

In this code you define four (4) variables that you will be working with:

* `five` - represents the Johnny-Five framework capabilities, which provide a type of object model for working with boards like Arduino and Edison.
* `Edison` - a variable that represents the edison-io plugin for Johnny-Five.
* `board` - a representation of the physical board you are using.
* `LEDPIN` - variable to hold the pin number value for the LED.