If you get connection errors when trying to connect to Azure IoT Hubs, it could be that the Edison operating system's date is innacurate. The Edison does a date/time sync when it starts, but some network firewalls may block the Network Time Protocols (NTP). If you are seeing errors, ensure the date is correct by executing the following code: 

{% highlight text %}
date -s "$(curl -sD - google.com | grep '^Date:' | cut -d' ' -f3-6)Z"`
{% endhighlight %}