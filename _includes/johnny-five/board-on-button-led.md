Johnny-Five provides a board ‘ready’ construct that makes a callback when the board is on, initialized and ready for action. Inside the anonymous callback function is where your application code executes (this function is invoked when the board is ready for use).

In the following code, you will create a callback function that is invoked when the board is initialized and ready (this is a Johnny-Five concept). In this lab, you will begin to work with Johnny-Five abstractions of the sensors and actuators - specifically `Led` and `Button`.

* You will create instances of each object type (`Led` and`Button`).
* You will handle the `Button.on('press')` and `Button.on('release')` events by defining the call back function for each. 
* You will change the state of the `Led` object in the callback functions.

In an earlier lab you likely used `digitalWrite(pin, value)` to set the pin state to 0 (LOW) or 1 (HIGH). In this lab, that code is abstracted into the `Led.off()` and `Led.on()` functions.

1. Add the following code to __button-led.js__

  {% highlight javascript %}
  // The board.on() executes the anonymous function when the
  // board reports back that it is initialized and ready. 
  board.on("ready", function() { 
    console.log("Board connected..."); 
      
    // Plug the LED module into the
    // Grove Shield's D6 jack.
    led = new five.Led(6);
    
    // Plug the Button module into the
    // Grove Shield's D4 jack.
    button = new five.Button(4);
    
    // *********************************************
    // The button.on('press') invokes the anonymous 
    // callback when the button is pressed.
    // *********************************************
    button.on('press', function() {
        console.log('PRESSED');
        led.on();
    });
    
    // *********************************************
    // The button.on('release') invokes the
    // anonymous callback when the button is
    // released.
    // *********************************************
    button.on('release', function() {
        console.log('RELEASED');
        led.off();
    });
  });
  {% endhighlight %}
  
