The application code resides in a JavaScript file in the same directory as the package.json file.

1. Create a file named __button-led.js__ in your lab application directory (e.g. _C:\Development\IoTLabs\MyLab_).

    Within the application file (button-led.js), you need to define the abstractions of the devices you will be using within the application. There are five (5) variables that matter:

    * A variable to reference the Johnny-Five framework object.
    * A variable to reference to the Edison-IO plugin object.
    * A variable to represent the physical board.
    * A variable to represent the physical button.
    * A variable to represent the physical LED.<br/><br/>

2. Within the _button-led.js_ file, add the following code:

    {% highlight javascript %}
    'use strict';
var five = require ("johnny-five"); 
var Edison = require("edison-io");

// Define the sensors you will use
var button, led;

// Define the board, which is an abstraction of the Intel Edison
var board = new five.Board({
    io: new Edison()
});
    {% endhighlight %}

In this code you define five (5) variables that you will be working with:

* `five` - represents the Johnny-Five framework capabilities, which provide a type of object model for working with boards like Arduino and Edison.
* `Edison` - a variable that represents the edison-io plugin for Johnny-Five.
* `board` - a representation of the physical board you are using.
* `button` - a represntation of the physical Button Sensor you are using.
* `led` - a represntation of the physical LED Module you are using.