Since you are building a Node.js application, you will use a __package.json__ file to define the application metadata and the application dependencies. This helps make the application more portable, enabling the dependencies to be installed anywhere you run the application (e.g. on any device you deploy it to). The package.json file is used to give information to __NPM__ to identify the project nd handle the project's dependencies. It can optionally contain other metadata, including a project description, the version of the project, license information, configuration data, etc.

The package.json is kept at the application root and requires two fields:

* Application name - The name must be less than or equal to 214 characters, may not start with a `.` or `_`, may not have uppercase letters in the name, and must not contain non-URL-safe characters.
* Application version.

Optionally, for these labs, you will include:

* The repository information where the source code is maintained - this will help you to identify the master source if you need to reference it.
* Application description - this will help you understand what each lab sample does.
* A pointer to the Github issues tracker for the lab samples (in case you find something you want to tell us about).
* A license specification (MIT).
* The main field, which specifies the JavaScript file to run if you execute `node .` in the application directory. 
* Dependencies, which are specified in a simple object that maps a package name to a version range.

Start by creating the basic package.json file in the application directory.

1. Using your favorite/preferred text/code editor, create a file in the directory you want to use for this lab (e.g. _C:\Development\IoTLabs\MyLab_) named __package.json__
2. Add the following code...

  {% highlight json %}
  {
    "name": "IoT-Labs",
    "version": "0.0.1",
    "repository": {
      "type": "git",
      "url": "https://github.com/ThingLabsIo/IoTLabs"
    },
    "bugs": {
      "url": "https://github.com/ThingLabsIo/IoTLabs/issues"
    }
    "description": "Hello, World using Node.js and Johnny-Five blinks an LED.",
    "main": "blinky.js",
    "license": "MIT",
    "dependencies": {
      "johnny-five": "latest"
    }
  }
  {% endhighlight %}