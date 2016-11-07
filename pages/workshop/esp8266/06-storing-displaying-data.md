---
layout: "page-fullwidth"
title: "Lab 06: Storing and Displaying IoT Data"
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

In this lab, you will build a data pipeline that captures the data coming into your Azure IoT Hub, processes the data with Azure Stream Analytics, and then routes the data to downstream services. You will also create one of those downstream services. Your service will consume the data and present it through an Azure Web App where it will be rendered as a real-time graph.

We will use the light data you are sending from [lab 05](../sending-d2c-messages) and deploy a web application to Azure that will render the data so you can visualize it even if you don't have access to PowerBI.

In an [earlier lab](../setup-azure-iot-hub/) you provisioned an Azure IoT Hub and in the [previous lab](../sending-d2c-messages/) you built a physical device...a _Thing_. You coded an application to collect
data from the device and send it to your IoT Hub. At the end of the previous lab you had data going into your IoT Hub but you weren't doing 
anything with the data. This lab will make use of that unused data.

# Using Stream Analytics to Process and Route IoT Data
Azure Stream Analytics is a service that does real-time data processing in the cloud. You will create a new Stream Analytics job and define the 
input data stream as the data coming from your IoT Hub. Next you will define an output data stream that sends data to an Event Hub (and optionally to PowerBI). Finally, you 
will write a SQL-like query that collects data coming in on the input stream and routes it to the output stream(s). 

## Create an Event Hub

First, you need to create an Event Hub to queue the data coming out of the Azure Stream Analytics Job. Open a new browser tab and navigate to the [https://manage.windowsazure.com](https://manage.windowsazure.com). Click on the __NEW__ icon in the lower-left corner.

![Windows Azure Portal v1](/images/photon_lab07_5.png)

1. Select __+__ > __APP SERVICES__ > __SERVICE BUS__ > __EVENT HUB__ > __QUICK CREATE__ and enter the following:
    - EVENT HUB NAME: You can use anything that is a valid name here, such as thinglabs-eventhub-[yourname, initials, etc]
    - REGION: If you created your IoT Hub in _East US_, select __East US 2__. For other regions, select the same region you created your IoT Hub in.
    - SUBSCRIPTION: Select the subscription you've created your resources in.
    - NAMESPACE: You can use anything that is a valid name here, such as thinglabs-eh-[yourname, initials, etc]
    
![Configure Event Hub](/images/ehquickcreate.png)

## Create the Stream Analytics Job
Next, you can create the Stream Analytics Job by opening a new browser tab and navigating to [https://manage.windowsazure.com](https://manage.windowsazure.com){:target="_blank"}. Login if necessary. Click on the __NEW__ icon in the lower-left corner.

1. Select __DATA SERVICES__ > __STREAM ANALYTICS__ > __QUICK CREATE__ and enter the following:
    - JOB NAME: You can use whatever name you like. (e.g. __iotlab__)
    - REGION: Select the same region you created your IoT Hub in. For example, if you created your IoT Hub in _East US_, select __East US 2__. 
    - REGIONAL MONITORING STORAGE ACCOUNT: Select or create a storage account.

    ![Defining a new Stream Analytics job](/images/newasa.png)

2. Click _CREATE STREAM ANALYTICS JOB_. It will take a few minutes for the Steam Analytics job to get created and become available. 

![Creating a new Stream Analytics job](/images/asajobcreated.png)

When the job indicates that it is created, click into it to create the data streams and query. Once you are in the Stream Analytics job you will need to define the job input, query, and output. 

### Define the Input Data Stream
The data will come in as a data stream from the Event Hub that was automatically created when you created the Azure IoT Hub. 

1. Click on the __Inputs__ section.

    ![Create the input](/images/azurestreaminput.png)

2. Click _+ Add_.
5. Complete the form as follows:
    - INPUT ALIAS - __DeviceInputStream__
    - SOURCE TYPE - _Data stream_
    - Source - _IoT hub_
    - SUBSCRIPTION - _Provide IoT hub settings manually_
    - IOT HUB - Type the name of the IoT Hub you created earlier.
    - ENDPOINT - _Messaging_
    - SHARED ACCESS POLICY NAME - Leave this as the default, which should be _iothubowner_.
    - SHARED ACCESS POLICY KEY - Type the primary key of the IoT Hub you created earlier.
    - CONSUMER GROUP - __AnalyticsConsumerGroup__
    - EVENT SERIALIZATION - _JSON_
    - ENCODING - _UTF-8_

6. Click on the Create button on the bottom of the page.

![Stream Analytics input definition](/images/azurestreaminputdetail.png)

After a few seconds, a new input will be listed.

### Define Output Data Streams
Before defining the query that will select data from the input and send it to the outputs you need to define the outputs. For this lab you will output the results of the query to an Azure Web Dashboard you'll create later and/or Power BI dataset for reporting.

You will create two outputs, one for data to flow to EventHub and a second for data to flow to PowerBI. 

To define  the EventHub output data stream, do the following:

1. Click on the _Outputs_ header.

    ![Create the output](/images/azurestreaminput.png)

2. Click on __+ Add__.
3. Complete the form as follows:
    - OUTPUT ALIAS - __ThingLabsOutput__
    - SINK - _Event hub_
    - SUBSCRIPTION - _Provide event hub settings manually_
    - SERVICE BUS NAMESPACE - Type the namespace of the service bus you created earlier.
    - EVENT HUB NAME - Type the name of the event hub you created earlier.
    - EVENT HUB POLICY NAME - _RootManageSharedAccessKey_
    - EVENT HUB POLICY KEY - Type the key of the Event Hub you created earlier.
    - PARTITION KEY COLUMN - You can leave this blank.
    - EVENT SERIALIZATION - _JSON_
    - ENCODING - _UTF-8_
    - FORMAT - _Line separated_

4. Click on the Create button on the bottom of the page.

![Stream Analytics output definition](/images/azurestreamoutputdetail.png)

### Write the Query
In the query, you want to select data from the input stream and put it into the output stream. With data like _darkness_, you can do interesting things like applying operations on the data as you query it. For this example, you will write a query that selects from the input stream and sends the output stream the minimum, maximum and average darkness values across all devices, and enables you to group the data by either location or device ID. Using a <code>TumblingWindow</code> you will send data to the output stream in rolling increments of 5-seconds.

1. Click on the _QUERY_ header.

    ![Create the query](/images/asaquery.png)

2. Write the following query. Make sure to update _DeviceInputStream_ and _ThingLabsOutput_ to the stream analytics input and output values you created in the previous steps.

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

SELECT * INTO [ThingLabsOutput] FROM ProcessedData
{% endhighlight %}

3. Click _SAVE_ in the lower middle of the screen. 
4. Once the query is saved, click _START_ to start the Stream Analytics job. 

If your app from the [previous lab](../sending-telemetry/) isn't still running, go ahead and start it up. It will take a few minutes for the Stream Analytics job to get started and to start sending data to Power BI, but you should see _MyIoTDataSet_ show up in Power BI within a few minutes. Remember, the _TumblingWindow_ is set to 5-seconds, so PowerBI will only update every 5-seconds.

## Create an Azure Website that Shows EventHub Data

You need to create a Web App to visualize the data coming out of the Event Hub. Click on the __NEW__ icon in the 
lower-left corner.

![Windows Azure Portal v1](/images/photon_lab07_5.png)

1. Select __+__ > __COMPUTE__ > __WEB APP__ > __QUICK CREATE__
    - URL: You can use anything that is a valid name here, such as thinglabs-eventhub-[yourname, initials, etc]
    - APP SERVICE PLAN: Select "Create a new App Service Plan".
    - REGION: Select the same region you created your IoT Hub in. For example, if you created your IoT Hub in _East US_, select __East US 2__.
    
    ![Configure Event Hub](/images/webappquickcreate.png)

2. Setup deployment from github:
    - On the web app configuration dashboard, click "Set up deployment from source control." (on the lower righthand side of the page)
    
    ![Configure Event Hub](/images/webappconfig1.png)
    
    - Select "External repository" from the dialog 
     
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

        - Use the name from your Event Hub: Add _THINGLABS_EVENTHUBNAME_ and the name of your event hub
    
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

<a class="radius button small" href="{{ site.url }}/workshop/esp8266/sending-c2d-messages/">Go to 'Lab 07: Sending Cloud-to-Device (C2D) Messages' ›</a>

[nextlab]: ../sending-c2d-messages/