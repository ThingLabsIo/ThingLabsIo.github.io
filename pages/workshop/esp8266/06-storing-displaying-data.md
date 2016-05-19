---
layout: "page-fullwidth"
title: "Storing and Displaying Data"
subheadline: "Building Connected Things with an ESP8266 and Microsoft Azure"
teaser: "In this lab you will build a data pipeline that captures the data coming into IoT Hub, processes it with Azure Stream Analytics, then routes it to downstream services. The first service you'll build will consume the data and present it through an Azure Web App where it is rendered as a real-time graph."
show_meta: true
comments: true
header: no
breadcrumb: true
categories: [esp8266, lua, MQTT, iot, maker, azure, azure-iot-hub]
permalink: /workshop/esp8266/storing-displaying-data/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will build a data pipeline that captures the data coming into IoT Hub, processes it with Azure Stream Analytics, then routes it to downstream services. The first service you'll build will consume the data and present it through an Azure Web App where it is rendered as a real-time graph.

We will use the light data you are sending from [lab 5](../sending-d2c-messages) and deploy a web application to azure that will render the data so you can visualize it even if you don't have access to PowerBI.

In an [earlier lab](../setup-azure-iot-hub/) you provisioned an Azure IoT Hub and in the [previous lab](../sending-d2c-messages/) you built a physical device...a _Thing_. You coded an application to collect
data from the device and send it to your IoT Hub. At the end of the previous lab you had data going into your IoT Hub but you weren't yet doing 
anything with it. Let's change that.

# Using Stream Analytics to Process and Route IoT Data
Azure Stream Analytics is a service that does real-time data processing in the cloud. You will create a new Stream Analytics job and define the 
input data stream as the data coming from your IoT Hub. Next you will define an output data stream that sends data to an Event Hub (and optionally to PowerBI). Finally, you 
will write a SQL-like query that collects data coming in on the input stream and routes it to the output stream(s). 

## Create an Event Hub

First, you need to create an Event Hub to queue the data coming out of the Azure Stream Analytics Job. Open a new browser tab and navigate to the [https://manage.windowsazure.com](https://manage.windowsazure.com). Click on the __NEW__ icon in the lower-left corner.

![Windows Azure Portal v1](/images/photon_lab07_5.png)

1. Select __+__ > __APP SERVICES__ > __SERVICE BUS__ > __EVENT HUB__ > __QUICK CREATE__ and enter the following:
    - EVENT HUB NAME: You can use anything that is a valid name here, such as thinglabs-eventhub-[yourname, initials, etc]
    - REGION: If you created your IoT Hub in _East US_, select __East US 2__. Select the same region you created your IoT Hub in.
    - SUBSCRIPTION: Select the subscription you've created your resources in.
    - NAMESPACE: You can use anything that is a valid name here, such as thinglabs-eh-[yourname, initials, etc]
    
![Configure Event Hub](/images/ehquickcreate.png)

## Create the Stream Analytics Job
Next, you can create the Stream Analytics Job by opening a new browser tab and navigate to [https://manage.windowsazure.com](https://manage.windowsazure.com){:target="_blank"}. Login if necessary. Click on the __NEW__ icon in the lower-left corner.

1. Select __DATA SERVICES__ > __STREAM ANALYTICS__ > __QUICK CREATE__ and enter the following:
    - JOB NAME: You can use anything you'd like here...like __iotlab__ or something similar so you can identify it easily later.
    - REGION: If you created your IoT Hub in _East US_, select __East US 2__. Select the same region you created your IoT Hub in.
    - REGIONAL MONITORING STORAGE ACCOUNT: Select or create a storage account.

    ![Defining a new Stream Analytics job](/images/newasa.png)

2. Click _CREATE STREAM ANALYTICS JOB_. It will take a few minutes for the Steam Analytics job to get created and become available. 

![Creating a new Stream Analytics job](/images/asajobcreated.png)

When the job indicates that it is created, click into it to create the data streams and query. Once you are in the Stream Analytics job you will need to define the job input, query, and output. 

### Define the Input Data Stream
The data will come in as a data stream from the Event Hub that was automatically created when you created the Azure IoT Hub. 

1. Click on the __INPUTS__ header.

    ![Create the input](/images/asainputs.png)

2. Click on _ADD AN INPUT_.
3. Select __Data stream__ and click on the forward arrow in the lower-right.
4. Select __IoT Hub__ and click on the forward arrow in the lower-right.
5. Complete the form as follows:
    - INPUT ALIAS - __DeviceInputStream__
    - SUBSCRIPTION - choose your subscription
    - CHOOSE AN IOT HUB - choose the IoT Hub you created earlier
    - IOT HUB SHARED ACCESS POLICY NAME - leave this as the default, which should be _iothubowner_
    - IOT HUB CONSUMER GROUP - select _Create a new consumer group_ and name it __AnalyticsConsumerGroup__

6. Click on the forward arrow in the lower-right.
7. On the _Serialization settings_ form, leave the defaults (Event Serialization Format:JSON and Encoding:UTF8) click on the checkmark in the lower-right. 

![Stream Analytics input definition](/images/asainputform.png)

After a few seconds, a new input will be listed.

### Define Output Data Streams
Before defining the query that will select data from the input and send it to the outputs you need to define the outputs. For this lab you will output the results of the query to an Azure Web Dashboard you'll create later and/or Power BI dataset for reporting.

You will create two outputs, one for data to flow to EventHub and a second for data to flow to PowerBI, so for EventHub:

1. Click on the _OUTPUTS_ header.

    ![Create the output](/images/asaoutputs.png)

2. Click on __ADD AN OUTPUT__.
3. Select __Event Hub__ and click on the forward arrow in the lower-right.

    ![Event Hub Output](/images/ehoutput.png)

4. Configure the EventHub

    ![Event Hub Configuration](/images/ehsettings.png)

    1. Give it a unique name
    2. Select "Use Event Hub from Current Subscription"
    3. From the drop-down, select your previously created Event Hub
    4. Click the right arrow to go to the "Serialization Settings"
    5. Accept the defaults (JSON, UTF8, Line Separated)
5. Click checkmark on the lower-right to create the Event Hub
6. Start the Event Hub by clicking the Start triangle at the bottom of the window

### Write the Query
In the query, you want to select data from the input stream and put it into the output stream. With data like _darkness_, you can do interesting things like applying operations on the data as you query it. For this example, you will write a query that selects from the input stream and sends the output stream the minimum, maximum and average darkness values across all devices, and enables you to group the data by either location or device ID. Using a <code>TumblingWindow</code> you will send data to the output stream in rolling increments of 5-seconds.

1. Click on the _QUERY_ header.

    ![Create the query](/images/asaquery.png)

2. Write the following query:

{% highlight sql %}
WITH ProcessedData as (
    SELECT
        MAX(celsius) MaxTemperature,
        MIN(celsius) MinTemperature,
        AVG(celsius) AvgTemperature,
        MAX(lightLevel) MaxLightLevel,
        MIN(lightLevel) MinLightLevel,
        AVG(lightLevel) AvgLightLevel,
        MAX(relativeHumidity) MaxRelativeHumidity,
        MIN(relativeHumidity) MinRelativeHumidity,
        AVG(relativeHumidity) AvgRelativeHumidity,
        location,
        deviceId,
        System.Timestamp AS Timestamp
    FROM
        [DeviceInputStream]
    GROUP BY
        TumblingWindow (second, 5), deviceId, location
)

-- If you do the optional lab you will need to uncomment this line
--   to get data to flow to PowerBI
-- SELECT * INTO [DeviceBI] FROM ProcessedData

-- Make sure this matches your Event Hub Name from above,
-- If you've forgotten it you can go back and get it in another browser tab
SELECT * INTO [ThingLabsEventHub] FROM ProcessedData
{% endhighlight %}

3. Click _SAVE_ in the lower middle of the screen. 
4. Once the query is saved, click _START_ to start the Stream Analytics job. 

If your app from the [previous lab](../sending-telemetry/) isn't still running, go ahead and start it up. It will take a few minutes for the Stream Analytics job to get started and to start sending data to Power BI, but you should see _MyIoTDataSet_ show up in Power BI within a few minutes. Remember, the _TumblingWindow_ is set to 5-seconds, so PowerBI will only update every 5-seconds.

## Create Azure Website that Shows EventHub Data

Content Goes here

You need to create a Web App to visualize the data coming out of the Event Hub. Click on the __NEW__ icon in the 
lower-left corner.

![Windows Azure Portal v1](/images/photon_lab07_5.png)

1. Select __+__ > __COMPUTE__ > __WEB APP__ > __QUICK CREATE__
    - URL: You can use anything that is a valid name here, such as thinglabs-eventhub-[yourname, initials, etc]
    - APP SERVICE PLAN: Select "Create a new App Service Plan".
    - REGION: If you created your IoT Hub in _East US_, select __East US 2__. Select the same region you created your IoT Hub in.
    
    ![Configure Event Hub](/images/webappquickcreate.png)

2. Setup deployment from github:
    - On the web app configuration dashboard, click "Set up deployment from source control." (on the lower righthand side of the page)
    
    ![Configure Event Hub](/images/webappconfig1.png)
    
    - Select "External repositiory" from the dialog 
     
     (Note: You'd think this should be "Github repository", but we're avoiding having you fork and maintain your own version fo the code for today. If you want to modify it later, you can fork the repository and modify it. Then you would have to update your deployment source to "Github Repository" which finds *your* repositories.)
     
    ![External Repository](/images/webapp-external-repository.png)
    
    - Navigate to the [ThingLabs Web App repository](https://github.com/ThingLabsIo/ThingLabs-IoT-Dashboard), copy the git repo url
    
    ![Git repo url](/images/thinglabs-github-webapp-repo.png)
    
    - Paste it into the dialog on the configuration (the External Repository Page)
    
    ![Git repo url](/images/webapp-config-repo.png)
    
    - Click checkmark on the lower-right to setup your deployment from the ThingLabs Github Repository
    - Click on "Configure" to customize settings for your the Web App.
        - Turn Web Sockets on
        
        ![Web App Enable Web Sockets](/images/WebAppConfigureWebSockets.png)
    
        - Add an App Setting (Key: THINGLABS_EVENTHUB_CONNSTRING Value: Connection String from your EventHub)
        
        ![Web App App Settings](/images/WebAppConfigureAppSettings.png)
    
        - Use the connection string from your Event Hub
        
        ![Event Hub Connection String](/images/EventHubConfigureConnectionString.png)
    
        - Save the changes
    - Restart the Web Application
3. Browse to your site
    - It will take a few minutes for data to flow, but you should start to see a graph render as data is recieved.
    
    ![Thing Labs Web Dashboard - Web App](/images/ThingLabs-Web-Dashboard.png)

## Optional: PowerBI Dashboard

If you have access to PowerBI you can build a similar real-time reporting dashboard in the [Building a PowerBI Dashboard](../06a-powerbi-dashboard).
<a class="radius button small" href="{{ site.url }}/workshop/thingy-4-windows/powerbi-dashboard/">Go to 'Building a PowerBI Dashboard' ›</a>
 
 
# Conclusion
In this lab you learned how to create an Azure Stream Analytics job to query data coming in to Azure IoT Hub, process it and send it to Event Hub and Power BI.

Congratulations! In this hands-on workshop you experienced an IoT solution end-to-end. You built a _Thing_ that both sent output (blinked an LED) and collected input (ambient light) and uased it to send data to Azure IoT Hubs. You then passed that data stream into Azure Stream Analytics, queried it and sent the output to Power BI where you created a visualization of the IoT data.

In the [next lab][nextlab] you will modify the web application to include a capability to send a Cloud-to-Device (C2D) message. 

<a class="radius button small" href="{{ site.url }}/workshop/esp8266/sending-c2d-messages/">Go to 'Sending Cloud-to-Device (C2D) Messages' ›</a>

[nextlab]: ../sending-c2d-messages/