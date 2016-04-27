---
layout: "page-fullwidth"
title: "Building Real-time Dashboards with PowerBI"
subheadline: "Seeing your data"
teaser: "In this lab you will build a PowerBI Dashboard to view your device data in real-time."
show_meta: true
comments: true
header: no
breadcrumb: true
categories: [raspberry-pi, windows-10, grove, c#, iot, maker, azure, azure-iot-hub]
permalink: /workshop/thingy-4-windows/powerbi/
---

## Define Output Data Stream (From Stream Analytics Job)
Before defining the query that will select data from the input and send it to the outputs you need to define the outputs. For this lab you will output the results of the query to an Azure Web Dashboard you'll create later and/or Power BI dataset for reporting.

You will create two outputs, one for data to flow to EventHub and a second for data to flow to PowerBI, so for EventHub:

1. Click on the _OUTPUTS_ header.

![Create the output](/images/asaoutputs.png)

1. Click on __ADD AN OUTPUT__.
2. Select __Power BI__ and click on the forward arrow in the lower-right.
3. Click "Authorize Now" to allow data to publish to PowerBI (Dialogs will appear)
4. After you have authorized the connection to Power BI, complete the form as follows:

 - OUTPUT ALIAS - __OutputAlias__
 - DATASET NAME - __MyIoTDataSet__
 - TABLE NAME - __MyIoTDataTable__
 - GROUP NAME - __My Workplace__

5. Click on the checkmark in the lower-right.

![Stream Analytics output definition](/images/asaoutputform.png)

![Stream Analytics output definition](/images/asaoutputform.png)

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