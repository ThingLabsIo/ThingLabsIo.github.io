---
layout: page-fullwidth
title: "Visualizing IoT Data"
subheadline: "Particle Photon IoT Lab 7"
teaser: "In this lab you will send some sensor data to Azure Event Hubs, process the data using Azure Stream Analytics and visualize the data using Power BI."
show_meta: true
comments: true
header: no
breadcrumb: true
categories:
    - iot-photon-labs
    - connected-things-101
permalink: "/photon/07/"
---
If you haven't already done so, please follow the instructions in [Lab 00: Getting Started][lab00] section.

### Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will send some sensor data to Azure Event Hubs, process the data using Azure Stream Analytics and visualize the data using Power BI.

## Bill of Materials
For this lab you need the same materials as [Lab 2][lab02]

1. [Particle Photon][photon]
2. USB to micro-USB cable (there is one included in the [Photon Development Kit][photon])
3. Photoresistor (there is one included in the [Photon Development Kit][photon])
4. 220-Ohm 1/4 Watt resistor (there is one included in the [Photon Development Kit][photon])
5. [Jumper wires](https://www.sparkfun.com/products/12795)

For this lab series you are using a Particle Photon, a small Wi-Fi enabled development board. The Photon is an excellent prototyping board for connected Things and Particle, the makers of the Photon, sell the P0 chip that drives the board (so when you are ready to go into production you can easily use the same chip). While you can develop for the Photon using Particle Build or Particle Dev and leverage the Particle Cloud, this lab series is designed to teach you how to build a Wi-Fi based hub-and-spoke system, similar to how SmartThings or Phillips Hue works. For the first few labs you will learn how to create Node.js applications that run on your PC and control the Photon. Later in the lab you will learn how to deploy the Node.js applications to a hub device, like a Raspberry Pi 2 or an Arduino Y&uacute;n, which will act as the field gateway for all of the connected Photons in your solution.

In this lab you will be sending telemetry into Azure Event Hubs and visualizing it using Power BI. TO simplify this lab you will not be using Nitrogen like you did in the preceeding three labs. The reason for that is that to use Nitrogen with Azure services, the Nitrogen instance has to be running in your account. Rather than takign the time to set up Nitrogen within your account, we will remove that layer (meaning that we no longer have some of the benefits of Nitrogen, such as command/control messages, etc.). When you are ready to build a complete solution that leverages Nitrogen you can refer to the Nitrogen setup labs (coming soon).

Here are the specific wiring instructions for the board.
<img src="/images/photon_lab02_bb.png"/>

### Photoresistor
Insert a photoresistor into the breadboard as shown in the diagram.

### Resistor
Connect a 220-Ohm resistor from one side of the photoresistor across a couple of rows.

### Wires
Connect the wires as shown in the diagram:

#### Red
Connect the 3.3V pin to the red/positive side-rail on the breadboard.
Connect the red/positive side-rail to the row where the resistor lead is connected but the photoresistor is not (this is the input voltage into the static resistor part of the voltage divider).

#### Green
Connect the green wire from the other side of the static resistor (this should be in the same row as the static resistor lead and one of the photoresistor leads) to the A0 pin on the Photon (this is one route of the voltage divider - the other route is through the photoresistor).

#### Black
Connect the row holding the other lead from the photoresistor to the black/negative side-rail on the breadboard.
Connect the black/negative side-rail of the breadboard to one of the GND pins on the Photon.
This completes the circuit.

## Prepare an Azure Event Hub
Using a browser navigate to [http://manage.windowsazure.com](http://manage.windowsazure.com). If you don't already have an Azure subscription you should set up a free trial.
 
In the lower left hand corner click on "NEW"

<img src="/images/photon_lab07_1.png"/>
  
Select _APP SERVICES_ > _SERVICE BUS_ > _EVENT HUB_ > _QUICK CREATE_

<img src="/images/photon_lab07_2.png"/>

Enter the following information:
1. __EVENT HUB NAME__: "iotlab"
2. __REGION__: Choose a region that is physically close to the location of the device that will be sending data to it.
3. __SUBSCRIPTION__: Choose the subscription that you want to use to pay for this Event Hub
4. __NAMESPACE NAME__: A unique name that will identify this Service Bus instance.
 
Once the Event Hub has been created click on the line with the name "iotlab"

<img src="/images/photon_lab07_3.png"/>

Click on the _CONFIGURE_ tab to create a shared access policy that will allow you to send messages to the event hub.

<img src="/images/photon_lab07_4.png"/>
  
The _POLICY NAME_ and the _PRIMARY KEY_ will be used later when you write code to generate the SAS token.

## Writing the Code
For this lab you will create a new file named <strong>lab07.js</strong> in the same directory as you did in the previous labs. There are no additional dependencies, so you don't need to make any changes to the package.json file.

In the lab07.js file start by declaring the key objects, including a variable for the analog pin number you will use (A0 or 0). THis is the exact same code as [Lab 2][lab02].

{% highlight javascript %}
// Define the Jonny Five and Spark-IO variables
var five = require ("johnny-five"),
    board, photoresistor;
var Spark = require("spark-io");
{% endhighlight %}

In order to send messages to Azure Event Hub you will need to require the <code>https</code> and <code>crypto</code> libraries.

{% highlight javascript %}
var https = require('https');
var crypto = require('crypto');
{% endhighlight %}

After the requirements are defined add variables to hold the event hub names details.

{% highlight javascript %}
var namespace = process.env.EH_NAMESPACE || 'YOUR NAMESPACE NAME HERE';
var hubName = process.env.EH_HOSTNAME || 'YOUR EVENT HUB NAME HERE';
var deviceName = process.env.PARTICLE_DEVICE || 'YOUR DEVICE ID OR ALIAS HERE';
{% endhighlight %}

Add another variable that concatenates the event hub information into a publisher uri that we can use to request a SAS token later on.

{% highlight javascript %}
var publisherUri = 'https://' + namespace + '.servicebus.windows.net' + '/' + hubName + '/publishers/' + deviceName + '/messages';
{% endhighlight %}
  
Define the Johnny FIve board object using Spark IO.

{% highlight javascript %}
// Define the Johnny Five board as your Particle Photon
var board = new five.Board({
  io: new Spark({
    token: process.env.PARTICLE_KEY || 'YOUR API KEY HERE',
    deviceId: process.env.PARTICLE_DEVICE || 'YOUR DEVICE ID OR ALIAS HERE'
  })
});
{% endhighlight %}

Next, define the callback function in the Johnny-Five board initialization. For this lab you will use the <code>analogRead()</code> function, which takes the analog pin number in as input and calls a callback function when input is read off the pin. In the callback function, simply write the data to the console.

{% highlight javascript %}
// The board.on() executes the anonymous function when the 
// Partile Photon reports back that it is initialized and ready.
board.on("ready", function(){
  console.log("Board connected...");
    
  // TODO 1: Get a SAS token here
  
  // Create a new 'photoresistor' hardware instance.
  photoresistor = new five.Sensor({
    pin: "A0",  // Analog pin 0
    freq: 1000  // Collect data once per second
  });
  
  // Inject the 'sensor' hardware into the Repl instance's context;
  // Allows direct command line access
  board.repl.inject({
    pot: photoresistor
  });
  
  // Define the callback function for the photoresistor reading
  // The freq value used when the photoresistor was defined
  // determines how often this is invoked, thus controlling
  // the frequency of Nitrogen messages.
  photoresistor.on('data', function() {
    // Capture the ambient light level from the photoresistor
    var lightLevel = this.value;
    var payload = '{\"_lightLevel\": ' + lightLevel + '}';
    
    console.log(payload);
        
    // TODO 2: Create and send Event Hubs Message
  });
});
{% endhighlight %}

At the bottom of the file, create a method for generating the SAS token. If you are interested you can learn more about the SAS token on [MSDN](https://msdn.microsoft.com/library/azure/dn170477.aspx).

{% highlight javascript %}
function createSasToken(uri, keyName, keyV)
{
  // Token expires in 24 hours
  var expiry = Math.floor(new Date().getTime()/1000+3600*24);
 
  var string_to_sign = encodeURIComponent(uri) + '\n' + expiry;
  var hmac = crypto.createHmac('sha256', keyValue);
  hmac.update(string_to_sign);
  var signature = hmac.digest('base64');
  var token = 'SharedAccessSignature sr=' + encodeURIComponent(uri) + '&sig=' + encodeURIComponent(signature) + '&se=' + expiry + '&skn=' + keyName;
 
  return token;
}
{% endhighlight %}

Now that you have created the <code>createSasToken</code> function you can go back to the <code>board.on()</code> and add the call to this method. Add the following where you have the <code>// TODO 1: Get a SAS token here</code> comment.

{% highlight javascript %}
  // Create the SAS token used for Event Hubs
  var sasToken = createSasToken(publisherUri, keyName, keyValue);
{% endhighlight %}

Next you need to create the message that will be sent to your Event Hub each time the <code>photoresistor.on()</code> function is called (once per second). Replace the <code>// TODO 2: Create and send Event Hubs Message</code> comment with the following:

{% highlight javascript %}
    // Define the metadata associated with the message 
    // being sent to Event Hubs
    var options = {
      hostname: namespace + '.servicebus.windows.net',
      port: 443,
      path: '/' + hubName + '/publishers/' + deviceName + '/messages',
      method: 'POST',
      headers: {
        'Authorization': sasToken,
        'Content-Length': payload.length,
        'Content-Type': 'application/atom+xml;type=entry;charset=utf-8'
      }
    };
    
    // Create an HTTP request and a callback for error conditions
    var req = https.request(options);
    req.on('error', function(e) {
      console.error(e);
    });
    
    // Write the message paylod to the request stream
    req.write(payload);
    // End/close the request
    req.end();
{% endhighlight %}

## Configure Stream Analytics
Navigate to the Azure portal and sign in with the same credentials you used to create the Event Hub
 
In the lower left hand corner click on "NEW"

<img src="/images/photon_lab07_5.png"/>

Create a new Stream Analytics Job in the same region that you used to create the event hub. Matching the region isn't required but it helps to reduce the latency.

<img src="/images/photon_lab07_6.png"/>

Navigate into the job and add a new input to your job

<img src="/images/photon_lab07_7.png"/>

<img src="/images/photon_lab07_8.png"/>

Choose the namespace that you used to build the event hub and then choose the event hub from the drop down list

<img src="/images/photon_lab07_9.png"/>
  
<img src="/images/photon_lab07_10.png"/>
  
Create Query that the data from the event hub will pass through. Normalize data into second bars with low, high and average for the second.

<img src="/images/photon_lab07_11.png"/>

Add an output to the job to send the results into Power BI where you can create some charts for the data.

<img src="/images/photon_lab07_12.png"/>

You will need to Authorize the sending of the data from your job into your Power BI account. If you don't have a Power BI account you can create one for free now. Authorization is simply a process of providing your Microsoft Account credentials when prompted.

<img src="/images/photon_lab07_13.png"/>

Now that all of the setup is complete start the job. It will take a minute or so for the job to start and for data to show up in your power bi instance. The default selections in the start screen will work to get the job started for this demo.

<img src="/images/photon_lab07_14.png"/>

## Create a Report in Power BI
Go to PowerBi.com and sign in using the same Microsoft Account that you used to authorize the Stream Analytics output. In the Datasets list you should see _lab07_ listed with a star next to it. If you don't see it right away you can wait a minute or so longer for the job to kick in.

<img src="/images/photon_lab07_15.png"/>

Click on _lab07_ to open the chart designer. In the chart tools panel on the left hand side select the Line Chart and then select each of the data fields.

<img src="/images/photon_lab07_16.png"/>
  
A chart will appear with readings from the light sensor. The visualization of the lines will be different than what is below but the basic structure of the chart should match.

<img src="/images/photon_lab07_17.png"/>

Click on the __Save__ button above the chart to save it with the name _Lab 07_. Now under the Reports menu on the left click on __Lab 07__. Move the mouse onto the chart and you will see a pin in the upper right hand corner.  Click on that pin to add the chart to your dashboard. Navigate to your dashboard and then wave your hand over the light sensor to see the new measurements reflect in the chart. Remember that we are sampling the sensor every one second and then aggregating the data into second bars so the chart isn't going to change as fast as you are moving your hand.
  
If you want to compare your code to the final solution you can see the code in GitHub [here](https://github.com/ThingLabsIo/IoTLabs/blob/master/Photon/Lab07/lab07.js).

 [photon]: https://store.particle.io/?product=photon-kit
 [lab02]: /photon/02/
 