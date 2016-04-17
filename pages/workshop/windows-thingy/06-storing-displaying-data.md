---
layout: "page-fullwidth"
title: "Storing and Displaying Data"
subheadline: "Building Connected Things with Windows 10 IoT and Microsoft Azure"
teaser: "In this lab you will build a data pipeline that captures the data coming into IoT Hub and persists it using Azure storage. You will then connect a website to Azure and retreive the data for display in a browser."
show_meta: true
comments: true
header: no
breadcrumb: true
categories: [raspberry-pi, windows-10, grove, c#, iot, maker, azure, azure-iot-hub]
permalink: /workshop/thingy-4-windows/storing-displaying-data/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will process the data that you are sending into Azure IoT Hub using Azure Stream Analytics so that you can visualize it using Power BI.

In an [earlier lab](../setup-azure-iot-hub/) you provisioned an Azure IoT Hub and in the [previous lab](../sending-d2c-messages/) you built a physical device...a _Thing_. You coded an application to collect
data from the device and send it to your IoT Hub. At the end of the previous lab you had data going into your IoT Hub but you weren't yet doing 
anything with it. Let's change that.

# Using Stream Analytics to Process and Route IoT Data
Azure Stream Analytics is a service that does real-time data processing in the cloud. You will create a new Stream Analytics job and define the 
input data stream as the data coming from your IoT Hub. Next you will define an output data stream that sends data to Power BI. Finally, you 
will write a SQL-like query that collects data coming in on the input stream and routes it to the output stream. 

## Create the Stream Analytics Job
Open a new browser tab and navigate to [https://manage.windowsazure.com](https://manage.windowsazure.com){:target="_blank"}. Login if necessary. Click on the __NEW__ icon in the 
lower-left corner.

![Windows Azure Portal v1](/images/photon_lab07_5.png)

1. Select __DATA SERVICES__ > __STREAM ANALYTICS__ > __QUICK CREATE__ and enter the following:

 - JOB NAME: You can use anything you'd like here...like __iotlab__ or something similar so you can identify it easily later.
 - REGION: If you created your IoT Hub in _East US_, select __East US 2__. For other regions, select the same region you created your IoT Hub in.
 - REGIONAL MONITORING STORAGE ACCOUNT: Select or create a storage account.

![Defining a new Stream Analytics job](/images/newasa.png)

2. Click _CREATE STREAM ANALYTICS JOB_. It will take a few minutes for the Steam Analytics job to get created and become available. 

![Creating a new Stream Analytics job](/images/asajobcreated.png)

When the job indicates that it is created, click into it to create the data streams and query. Once you are in the Stream Analytics job you will need to define the job input, query and output. 

## Define the Input Data Stream
The data will come in as a data stream from the Event Hub that was automatically created when you created the Azure IoT Hub. 

1. Click on the __INPUTS__ header.

![Create the input](/images/asainputs.png)

1. Click on _ADD AN INPUT_.
2. Select __Data stream__ and click on the forward arrow in the lower-right.
3. Select __IoT Hub__ and click on the forward arrow in the lower-right.
4. Complete the form as follows:

 - INPUT ALIAS - __DeviceInputStream__
 - SUBSCRIPTION - choose your subscription
 - CHOOSE AN IOT HUB - choose the IoT Hub you created earlier
 - IOT HUB SHARED ACCESS POLICY NAME - leave this as the default, which should be _iothubowner_
 - IOT HUB CONSUMER GROUP - select _Create a new consumer group_ and name it __AnalyticsConsumerGroup__

5. Click on the forward arrow in the lower-right.
6. On the _Serialization settings_ form, leave the defaults (Event Serialization Format:JSON and Encoding:UTF8) click on the checkmark in the lower-right. 

![Stream Analytics input definition](/images/asainputform.png)

After a few seconds, a new input will be listed.

## Define Output Data Streams
Before defining the query that will select data from the input and send it to the outputs you need to define the outputs. For this lab you will output the results of the query to an Azure Web Dashboard you'll create later and/or Power BI dataset for reporting.

You will create two outputs, one for data to flow to EventHub and a second for data to flow to PowerBI, so for EventHub:

1. Click on the _OUTPUTS_ header.

![Create the output](/images/asaoutputs.png)

1. Click on __ADD AN OUTPUT__.
2. Select __EventHub__ and click on the forward arrow in the lower-right.
3. ...
4. After you have authorized the connection to Power BI, complete the form as follows:

 - OUTPUT ALIAS - __OutputAlias__
 - DATASET NAME - __MyIoTDataSet__
 - TABLE NAME - __MyIoTDataTable__
 - GROUP NAME - __My Workplace__

5. Click on the checkmark in the lower-right.

![Stream Analytics output definition](/images/asaoutputform.png)

Then for Power BI:

1. Click on the _OUTPUTS_ header.

![Create the output](/images/asaoutputs.png)

1. Click on __ADD AN OUTPUT__.
2. Select __Power BI__ and click on the forward arrow in the lower-right.
3. Follow the instructions for either _Existing Microsoft Power BI User_ or _New User_ using your __@microsoft.com__ email account.

<blockquote>
Power BI is a data visualization toolkit for organizations. To create a new user account, you will have to use an account that belongs to an 
organization, such as your place of employment. You will not be able to create a new user account using an email address that ends in 
Outlook.com, Hotmail.com, GMail.com or other general email provider accounts.
</blockquote>

4. After you have authorized the connection to Power BI, complete the form as follows:

 - OUTPUT ALIAS - __DeviceBI__
 - DATASET NAME - __MyIoTDataSet__
 - TABLE NAME - __MyIoTDataTable__
 - GROUP NAME - __My Workplace__

5. Click on the checkmark in the lower-right.

![Stream Analytics output definition](/images/asaoutputform.png)

### Write the Query
In the query, you want to select data from the input stream and put it into the output stream. With data like _darkness_ you can do interesting things like apply operations on the data as you query it. For this example, you will write a query that selects from the input stream and sends the output stream the minimum, maximum and average darkness values across all devices, and enables you to group the data by either location or device ID. Using a <code>TumblingWindow</code> you will send data to the output stream in rolling increments of 5-seconds.

1. Click on the _QUERY_ header.

![Create the query](/images/asaquery.png)

2. Write the following query:

{% highlight sql %}
SELECT
    MAX(messurementValue) MaxDark,
    MIN(messurementValue) MinDark,
    AVG(messurementValue) AvgDark,
    location,
    deviceId,
    System.Timestamp AS Timestamp
INTO
    [DeviceBI]
FROM
    [DeviceInputStream]
WHERE
    [messurementType] = 'darkness'
GROUP BY
    TumblingWindow (second, 5), deviceId, location 
{% endhighlight %}

3. Click _SAVE_ in the lower middle of the screen. 
4. Once the query is saved, click _START_ to start the Stream Analytics job. 

If your app from the [previous lab](../sending-telemetry/) isn't still running, go ahead and start it up. It will take a few minutes for the Stream Analytics job to get started and to start sending data to Power BI, but you should see _MyIoTDataSet_ show up in Power BI within a few minutes. Remember, the _TumblingWindow_ is set to 5-seconds, so PowerBI will only update every 5-seconds.

# Create Azure Website that Shows EventHub Data


# Build Reports in Power BI
Go back to the browser tab where you have Power BI open. Look in the _Datasets_ node in the left-hand navigation. The _MyIoTDataSet_ should appear there within a few minutes of IoT Hub data streaming into the Stream Analytics job. 

1. Click on the _MyIoTDataSet_ dataset to open the report designer.
2. Select the __Line__ chart from the _Visualizations_ toolbox on the right side.
3. Select __maxdark__ to set it as the _Value_
4. Click on the dropdown arrow for _maxdark_ in the _Values_ box and select __Maximum__
5. Select __timestamp__ to set it as the _Axis_ 

Repeat steps 2-4 for __avgdark__ and __mindark__, changing their field type to __Average__ and __Minimum__ respectively.

![Create the Power BI report](/images/rpi2/powerbi01.png)

As you are setting the values you should see the line chart updating with the changes. Click _File_ > _Save_ and give the report the name __Darkness Report__. Hover over the upper-right corner of the chart and click on the pin icon. Create a new dashboard to pin the gauges to. Once you have pinned all three gauges, click on the dashboard in the left sidebar. On the dashboard you can watch the data update in near real-time. While you are watching the dashboard, pinch or blow on the sensors on the weather shield and you will see the data change in the gauges.

# Conclusion
In this lab you learned how to create an Azure Stream Analytics job to query data coming in to Azure IoT Hub, process it and send it to Event Hub and Power BI.

Congratulations! In this hands-on workshop you experienced an IoT solution end-to-end. You built a _Thing_ that both sent output (blinked an LED) and collected input (ambient light) and uased it to send data to Azure IoT Hubs. You then passed that data stream into Azure Stream Analytics, queried it and sent the output to Power BI where you created a visualization of the IoT data.

In the [next lab][nextlab] you will modify the web application to include a capability to send a Cloud-to-Device (C2D) message. 

<a class="radius button small" href="{{ site.url }}/workshop/thingy-4-windows/sending-c2d-messages/">Go to 'Sending Cloud-to-Device (C2D) Messages' ›</a>

[nextlab]: ../sending-c2d-messages/