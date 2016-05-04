By now, you should be familiar with handling the Johnny-Five `board.on('ready')` callback. In the following code, you will instantiate the physical devices, and then define the callback functions for the `button` and `temperature` sensors.

* You will create instances of each object type (`Led`, `Button`, `LCD`, and `Temperature`).
* You will handle the `Button.on('press')`, `Button.on('release')`, and `Temperature.on('data')` events by defining the callback function for each. 
* You will change the state of the `Led` object based on the `Button` state.
* You will change the state of the `LCD` object based on the `Temperature` state.

1. Add the following code to __thingy.js__

  {% highlight javascript %}
// *********************************************
// The board.on() executes the anonymous
// function when the 'board' reports back that
// it is initialized and ready.
// *********************************************
board.on('ready', function() {
    console.log('Board connected...');
    
    // Plug the Temperature sensor module
    // into the Grove Shield's A0 jack
    // The controller defines the type of 
    // Temperature sensor this is.
    temperature = new five.Thermometer({
        controller: "GROVE",
        pin: "A0"
    });
    
    // Plug the LCD module into any of the
    // Grove Shield's I2C jacks.
    // The controller specifies the type of LCD this is.
    lcd = new five.LCD({
        controller: 'JHD1313M1'
    });
    
    // Plug the LED module into the
    // Grove Shield's D6 jack.
    led = new five.Led(13);
    
    // Plug the Button module into the
    // Grove Shield's D4 jack.
    button = new five.Button(4);
    
    // *********************************************
    // The thermometer object will invoke a callback
    // everytime it reads data as fast as every 25ms
    // or whatever the 'freq' argument is set to.
    // *********************************************
    temperature.on('data', function() {
        // TODO - Add callback operation here
    });
    
    // *********************************************
    // The button.on('press') invokes the anonymous 
    // callback when the button is pressed.
    // *********************************************
    button.on('press', function() {
        led.on();
        console.log('PRESSED');
    });
    
    // *********************************************
    // The button.on('release') invokes the
    // anonymous callback when the button is
    // released.
    // *********************************************
    button.on('release', function() {
        led.off();
        console.log('RELEASED');
    });
});
  {% endhighlight %}

Most of the preceeding code should seem familiar to you by now. The new bit that you still need to do is handle the `Multi.on('data')` callback function.

When you initialized the _Temperature_ sensor, you specified a `controller` value (`GROVE`). This defines which _Temperature_ sensor type to instantiate - basically a driver. The __GROVE__ controller specifies the simple Grove Temperature sensor (there are others, including the popular TMP36). By specifying the _GROVE_ controller you are telling Johnny-Five how to interact with this sensor.

The callback function for the Temperature sensor is invoked every 25ms (the default value). You can change the frequency the callback is invoked by adding a `freq:` argument when the object is defined (similar to how you defined the `controller` and `pin`) and specifying the frequency in milliseconds (e.g. a value of 1000 would invoke the callback once per second). 

1. Repace the `// TODO` comment with the following code:

  {% highlight javascript %}
// Set the state of the variables based on the 
// value read from the thermometer
// 'this' scope is the thermometer
tempC = this.celsius;
tempF = this.fahrenheit;
  {% endhighlight %}

This will collect the current reading from the 