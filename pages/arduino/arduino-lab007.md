---
layout: page-fullwidth
title: "Visualizing IoT Data"
subheadline: "Arduino + Azure IoT Lab 7"
teaser: "In this lab you will create visualizations of IoT data."
show_meta: true
comments: true
header: no
breadcrumb: true
categories:
    - iot-arduino-labs
    - maker-101
author: "Doug Seven"
permalink: "/arduino/07/"
---
### Table of Contents
*  Auto generated table of contents
{:toc}

If you haven't already done so, please follow the instructions in [Lab 00: Getting Started](../00/) section.

In this lab you will process the data that you are sending into Azure IoT Hub using Azure Stream Analytics so that you can visualize it using Power BI.

## Bill of Materials
What you will need:

1. [Arduino Uno][uno] or [Arduino Y&uacute;n][yun] 
2. USB to micro-USB cable
3. [5mm Green LED](http://www.sparkfun.com/products/12062)
4. [330-Ohm 1/4 Watt resistor](http://www.sparkfun.com/products/10969) (Orange-Orange-Brown)
5. [Photoresistor (5528)](http://www.sparkfun.com/products/9088)
6. [10k-Ohm 1/4 Watt resistor](http://www.sparkfun.com/products/10969) (Brown-Black-Orange)

In the previous labs you provisioned an Azure IoT Hub and a physical device, and you wrote a Node.js application to collect data from the device 
and send it to your IoT Hub. At the end of the [previous lab](../06/) you had data going into your IoT Hub but you weren't yet doing anything with 
it. Let's change that.

## Using Stream Analytics to Process and Route IoT Data
Azure Stream Analytics is a service that does real-time data processing in the cloud. You will create a new Stream Analytics job and define the 
input data stream as the data coming from your IoT Hub. Next you will define an output data stream that sends data to Power BI. Finally, you 
will write a SQL-like query that collects data coming in on the input stream and routes it to the output stream. 

### Create the Stream Analytics Job
Open a new browser tab and navigate to the [https://manage.windowsazure.com](https://manage.windowsazure.com). Click on the __NEW__ icon in the 
lower-left corner.

<img src="/images/photon_lab07_5.png"/>

Select __DATA SERVICES__ > __STREAM ANALYTICS__ > __QUICK CREATE__ and enter the following:

1. JOB NAME: You can use anything you'd like here...I recommend __iotlab__ or something similar so you can identify it easily later.
2. REGION: Select __East US__ (or whatever region you created your IoT Hub in)
3. REGIONAL MONITORING STORAGE ACCOUNT: Select or create a storage account.

<img src="/images/newasa.png"/>

Click __CREATE STREAM ANALYTICS JOB__. It will take a few minutes for the Steam Analytics job to get created and become available. 

<img src="/images/asajobcreated.png"/>

When the job indicates that it is created, click into it to create the data streams and query.

### Define the Input Data Stream

Once you are in the Stream Analytics job, click on the __INPUTS__ header.

<img src="/images/asainputs.png"/>

Click on __ADD AN INPUT__.
Select __Data stream__ and click on the forward arrow in the lower-right.
Select __IoT Hub__ and click on the forward arrow in the lower-right.
Complete the form as follows:

1. INPUT ALIAS - _DeviceInputStream_
2. SUBSCRIPTION - choose your subscription
3. CHOOSE AN IOT HUB - choose the IoT Hub you created earlier
4. IOT HUB SHARED ACCESS POLICY NAME - leave this as the default, which should be _iothubowner_
5. IOT HUB CONSUMER GROUP - select _Create a new consumer group_ and name it _AnalyticsConsumerGroup_

Click on the forward arrow in the lower-right.

On the __Serialization settings__ form, leave the defaults (Event Serialization Format:JSON and Encoding:UTF8) click on the checkmark in the lower-right. 

<img src="/images/asainputform.png"/>

After a few seconds, a new input will be listed.

### Define the Output Data Stream
Click on the __OUTPUTS__ header.

<img src="/images/asaoutputs.png"/>

Click on __ADD AN OURPUT__.
Select __Power BI__ and click on the forward arrow in the lower-right.
Follow the instructions for either __Existing Microsoft Power BI User__ or __New User__

<blockquote>
Power BI is a data visualization toolkit for organizations. To create a new user account, you will have to use an account that belongs to an 
organization, such as your place of employment. You will not be able to create a new user account using an email address that ends in 
Outlook.com, Hotmail.com, GMail.com or other general email provider accounts.
</blockquote>

After you have authorized the connection to Power BI, complete the form as follows:

1. OUTPUT ALIAS - DeviceBI
2. DATASET NAME - MyIoTDataSet
3. TABLE NAME - MyIoTDataTable
4. GROUP NAME - My Workplace

Click on the checkmark in the lower-right.

<img src="/images/asaoutputform.png"/>

### Write the Query

Click on the __QUERY__ header.

<img src="/images/asaquery.png"/>

In the query, you want to select data from the input stream and put it into the output stream. With data like darkness you can do interesting things 
like apply operations on the data as you query it. For this example, you will write a query that selects from the input stream and send the output 
stream the minimum, maximum and average darkness values, and enables you to group the data by either location or device ID. 
Using a <code>TumblingWindow</code> you will send data to the output stream in rolling increments of 1-seconds.

Write the following query:

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
    TumblingWindow (second, 10), deviceId, location 
{% endhighlight %}

Click __SAVE__ in the lower middle of the screen. Once the query is saved, click __START_ to start the Stream Analytics job. If your Node.js app 
from the [previous lab](../06/) isn't still running, go ahead and start it up. It will take a few minutes for the Stream Analytics job to get 
started and to start sending data to Power BI, but you should see the _TemperatureDataSet_ show up in Power BI within a few minutes. Remember, 
the _TumblingWindow_ is set to 10-seconds, so PowerBI will only update every 10-seconds.

## Build Reports in Power BI

Go back to the browser tab where you have Power BI open. Look in the _Datasets_ node in the left-hand navigation. The _TemperatureDataSet_ should appear there within a few minutes of IoT Hub data streaming into the Stream Analytics job. 

1. Click on the _MyIoTDataSet_ dataset to open the report designer.
2. Select the _Line_ chart from the __Visualizations__ toolbox on the right side.
3. Select __maxdark__ to set it as the _Value_
4. Click on the dropdown arrow for _maxdark_ in the _Values_ box and select __Maximum__

Repeat steps 2-4 for __avgdark__ and __mindark__, changing their field type to __Average__ and __Minimum__ respectively.

<img src="/images/powerbi01.png"/>

As you are setting the values you should see the line chart updating with the changes. Click __File__ > __Save_ and give the report the 
name __Darkness Report__. Hover over the upper-right corner of the chart and click on the pin icon. Create a new dashboard to pin the gauges to. 
Once you have pinned all three gauges, click on the dashboard in the left sidebar. On the dashboard you can watch the data update in near real-time. 
While you are watching the dashboard, pinch or blow on the sensors on the weather shield and you will see the data change in the gauges.

## Conclusion &amp; Next Steps
Congratulations! In this lab you learned how to create an Azure Stream Analytics job to query data coming in to Azure IoT Hub, process it and send 
it to Power BI. In Power BI you learned how to create reports and pin the data visualizations to a dashboard for near real-time updates.

{% include next-previous-post-in-category.html %}

[uno]: http://www.arduino.cc/en/Main/ArduinoBoardUno
[yun]: http://www.arduino.cc/en/Main/ArduinoBoardYun