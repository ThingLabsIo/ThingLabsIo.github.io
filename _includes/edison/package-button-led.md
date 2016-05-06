Start by creating a new application directory and a new package.json file.

1. Using your favorite/preferred text/code editor, create a file in the directory you want to use for this lab (e.g. _C:\Development\IoTLabs\MyLab_) named __package.json__
2. Add the following code...

  {% highlight json %}
  {
    "name": "button-led",
    "version": "0.0.1",
    "repository": {
      "type": "git",
      "url": "https://github.com/ThingLabsIo/IoTLabs"
    },
    "bugs": {
      "url": "https://github.com/ThingLabsIo/IoTLabs/issues"
    },
    "description": "Responding to sensor input using Node.js, Johnny-Five, a Grove Button Sensor and LED Module.",
    "main": "button.js",
    "license": "MIT",
    "dependencies": {
      "johnny-five": "latest",
      "edison-io": "latest"
    }
  }
  {% endhighlight %}