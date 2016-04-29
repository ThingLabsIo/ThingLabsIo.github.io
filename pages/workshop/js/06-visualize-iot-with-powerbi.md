---
layout: page-fullwidth
title: "Visualizing IoT Data with Power BI"
subheadline: "Node.js - Connected Weather Station"
teaser: "In this lab you will create visualizations of IoT data using Microsoft Azure Stream Analytics and Power BI."
show_meta: true
comments: true
header: no
breadcrumb: true
categories: [azure, azure-iot-hub, javascript, node.js, johnny-five, arduino, photon]
permalink: /workshop/js/weather/visualize-iot-with-powerbi/
---
# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will process the data that you are sending into Azure IoT Hub using Azure Stream Analytics so that you can visualize it using Power BI.

In the previous labs you provisioned an Azure IoT Hub and a physical device, and you wrote a Node.js application to collect data from the board and send it to your IoT Hub. At the end of the [previous lab](../sending-telemetry/) you had data going into your IoT Hub but you weren't yet doing anything with it. Let's change that.

# Using Stream Analytics to Process and Route IoT Data

Azure Stream Analytics is a service that does real-time data processing in the cloud. You will create a new Stream Analytics job and define the input data stream as the data coming from your IoT Hub. Next you will define an output data stream that sends data to Power BI. Finally, you will write a SQL-like query that collects data coming in on the input stream and routes it to the output stream.

## Create the Stream Analytics Job

Open a new browser tab and navigate to the [https://manage.windowsazure.com](https://manage.windowsazure.com){:target="_blank"}. Click on the __NEW__ icon in the lower-left corner.

![New](/images/photon_lab07_5.png)

Select __DATA SERVICES__ > __STREAM ANALYTICS__ > __QUICK CREATE__ and enter the following:

1. JOB NAME: You can use anything you'd like here...I recommend __iotlab__ or something similar so you can identify it easily later.
2. REGION: Select __East US__ (or whatever region you created your IoT Hub in) __NOTE:__ If you are using a new Azure Account and experience errors, try creating the Stream Analytics job in the __East US 2__ region
3. REGIONAL MONITORING STORAGE ACCOUNT: Select or create a storage account.

![New Stream Analytics](/images/newasa.png)

Click __CREATE STREAM ANALYTICS JOB__. It will take a few minutes for the Steam Analytics job to get created and become available.

![Job created](/images/asajobcreated.png)

When the job indicates that it is created, click into it to create the data streams and query.

## Define the Input Data Stream

Once you are in the Stream Analytics job, click on the __INPUTS__ header.

![Inputs](/images/asainputs.png)

1. Click on __ADD AN INPUT__.
2. Select __Data stream__ and click on the forward arrow in the lower-right.
3. Select __IoT Hub__ and click on the forward arrow in the lower-right.
4. Complete the form as follows:

    - INPUT ALIAS - _DeviceInputStream_
    - SUBSCRIPTION - choose your subscription
    - CHOOSE AN IOT HUB - choose the IoT Hub you created earlier
    - IOT HUB SHARED ACCESS POLICY NAME - leave this as the default, which should be _iothubowner_
    - IOT HUB CONSUMER GROUP - select _Create a new consumer group_ and name it _AnalyticsConsumerGroup_

5. Click on the forward arrow in the lower-right.

On the __Serialization settings__ form, leave the defaults (Event Serialization Format:JSON and Encoding:UTF8):

1. Click on the checkmark in the lower-right.

![Input form](/images/asainputform.png)

After a few seconds, a new input will be listed.

## Define the Output Data Stream

Click on the __OUTPUTS__ header.

![Ouputs](/images/asaoutputs.png)

1. Click on __ADD AN OUTPUT__.
2. Select __Power BI__ and click on the forward arrow in the lower-right.
3. Follow the instructions for either __Existing Microsoft Power BI User__ or __New User__ using an organizational (aka your work) email account.

<blockquote>
Power BI is a data visualization toolkit for organizations. To create a new user account, you will have to use an account that belongs to an organization, such as your place of employment. You will not be able to create a new user account using an email address that ends in Outlook.com, Hotmail.com, GMail.com or other general email provider accounts.
</blockquote>

1. After you have authorized the connection to Power BI, complete the form as follows:

    - OUTPUT ALIAS - WeatherBI
    - DATASET NAME - WeatherDataSet
    - TABLE NAME - WeatherTable
    - GROUP NAME - My Workplace

2. Click on the checkmark in the lower-right.

![Output form](/images/asaoutputform.png)

## Write the Query
In the query, you want to select data from the input stream and put it into the output stream. With data like _fahrenheit_ you can do interesting things like apply operations on the data as you query it. For this example, you will write a query that selects from the input stream and sends the output stream the minimum, maximum and average fahrenheit values across all devices in a five-second tumbling window, and enables you to group the data by either location or device ID. Using a <code>TumblingWindow</code> you will send data to the output stream in rolling increments of five-seconds.

1. Click on the __QUERY__ header.

![Query](/images/asaquery.png)

In the query, you want to select data from the input stream and put it into the output stream. With data like temperature and humidity you can do interesting things like apply operations on the data as you query it. For this example, you will write a query that selects from the input stream and send the output stream the minimum, maximum and average values from the data coming in, and enables you to group the data by either location or device ID. Using a <code>TumblingWindow</code> you will send data to the output stream in rolling increments of 10-seconds.
Write the following query:

{% highlight sql %}
SELECT
    MAX(fahrenheit) MaxTempF,
    MIN(fahrenheit) MinTempF,
    AVG(fahrenheit) AvgTempF,
    MAX(celsius) MaxTempC,
    MIN(celsius) MinTempC,
    AVG(celsius) AvgTempC,
    MAX(relativeHumidity) MaxHumidity,
    MIN(relativeHumidity) MinHumidity,
    AVG(relativeHumidity) AvgHumidity,
    location,
    deviceId,
    System.Timestamp AS Timestamp
INTO
    [WeatherBI]
FROM
    [DeviceInputStream]
GROUP BY
    TumblingWindow (second, 5), deviceId, location
{% endhighlight %}

1. Click __SAVE__ in the lower middle of the screen. 
2. Once the query is saved, click __START__ to start the Stream Analytics job. 
3. If your Node.js app from the [previous lab](../sending-telemetry/) isn't still running, go ahead and start it up. 
4. Open [PowerBI.com](http://www.powerbi.com){:target="_blank"} and login with the same credantials you used to authorize the Stream Analytics output.

It will take a few minutes for the Stream Analytics job to get started and to start sending data to Power BI, but you should see the _WeatherDataSet_ show up in Power BI within a few minutes.

# Build Reports in Power BI
Go back to the browser tab where you have Power BI open. Look in the _Datasets_ node in the left-hand navigation. The _TemperatureDataSet_ should appear there within a few minutes of IoT Hub data streaming into the Stream Analytics job.

1. Click on the _WeatherDataSet_ dataset to open the report designer.
2. Select the __Line__ chart from the _Visualizations_ toolbox on the right side.
3. Select __maxtempf__ to set it as the _Value_
4. Click on the dropdown arrow for __maxtempf__ in the _Values_ box and select __Maximum__
5. Select __timestamp__ to set it as the _Axis_ 

Repeat steps 2-4 for __avgtempf__ and __mintempf__, changing their field type to __Average__ and __Minimum__ respectively.

![Power BI](/images/powerbi-linechart.png)

As you are setting the values you should see the line chart updating with the changes. Click __File__ > __Save__ and give the report the name __Temperature Report__. Hover over the upper-right corner of each gauge and click on the pin icon. Create a new dashboard to pin the gauges to. Once you have pinned all three gauges, click on the dashboard in the left sidebar. On the dashboard you can watch the data update in near real-time (remember, you set up a tumbling window for 5-seconds, so you will only see updates once every 5-seconds). While you are watching the dashboard, pinch or blow on the sensors on the weather shield and you will see the data change in the gauges.

# Conclusion &amp; Next Steps
Congratulations! In this lab you learned how to create an Azure Stream Analytics job to query data coming in to Azure IoT Hub, process it and send it to Power BI. In Power BI you learned how to create reports and pin the data visualizations to a dashboard for near real-time updates.
