Johnny-Five provides a board 'ready' construct that makes a callback when the board is on, initialized and ready for action. Inside the anonymous callback function is where your application code executes (this function is invoked when the board is ready for use). Johnny-Five provides a collection of objects that represent the board, the pins on the board, and various types of sensors and devices that could be connected to the board. For this lab you are going to write code that is fairly true to the base Arduino sketch programming model (we'll get into the abstractions that Johnny-Five provides you later). This will help you understand some of the basic concepts for how prototyping boards works.

In the following code you will create a callback function that is invoked when the board is initialized and ready (this is a Johnny-Five concept). 

* You will set digital pin (the `LEDPIN` variable above) as an output pin (vs. an input pin), meaning the application is expecting to send voltage out from the pin as opposed to read the voltage coming in to the pin.
* You will create a loop that runs once per second.
* Inside that loop you will write out to the pin either LOW or HIGH voltage. Since the pin is a digital pin, its only options are 0 and 1 - in the world of Arduino (and similar) boards that is LOW and HIGH. When you send 0 (or LOW) to the pin, that is equivalent to off (sending no voltage). When you send 1 (or HIGH) to the pin that is equivalent to on (sending full voltage).

1. Add the following code to __blinky.js__

  {% highlight javascript %}
  // The board.on() executes the anonymous function when the
  // board reports back that it is initialized and ready. 
  board.on("ready", function() { 
    console.log("Board connected..."); 
      
    // Set the pin you connected to the LED to OUTPUT mode  
    this.pinMode(LEDPIN, five.Pin.OUTPUT); 

    // Create a loop to "flash/blink/strobe" an led  
    var val = 0;
    this.loop( 1000, function() {
      this.digitalWrite(LEDPIN, (val = val ? 0 : 1));
    });
  });
  {% endhighlight %}
  
Johnny-Five actually has an object model for an LED and we could also have simply done the following, but one of the goals was for you to see how the __digitalWrite()__ function works before abstracting it away.

{% highlight javascript %}
board.on("ready", function() {
	var led = new five.Led(LEDPIN);
	led.blink(1000);
});
{% endhighlight %}