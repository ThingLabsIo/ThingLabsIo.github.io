The Intel Edison requires one additional dependency - a plugin for Johnny-Five named __edison-io__. This is the plugin the maps the Johnny-Five abstractions to the physical board architecture. To add the edison-io dependency you can use NPM.

1. From the terminal window (Mac OS X) or Node.js command prompt (Windows) in your application directory, execute the following command:

{% highlight text %}
npm install edison-io --save
{% endhighlight %}

This will install the edison-io package and add it to the dependencies list in your package.json file. Open the package.json file to verify it was added.