---
layout: "page-fullwidth"
title: "Bot Builder for Node.js Workshop"
subheadline: "Building Connected Things with the Bot Framework for Node.js"
show_meta: true
comments: false
header: 
    image_fullwidth: workshops/thingy-4-windows/header.jpeg
breadcrumb: true
categories: [node, bot-builder]
permalink: /workshop/dev-days/js-bot/luis-question/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will integrate LUIS model intou your _Bot_ so your bot can answer specific questions.

# Modify the Code in an App.js File

Replace the existing code in the App.js file with the code below.

{% highlight javascript %}
var restify = require('restify');
var builder = require('botbuilder');

//=========================================================
// Bot Setup
//=========================================================

// Setup Restify Server
var server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3798, function () {
   console.log('%s listening to %s', server.name, server.url); 
});
  
// Create chat bot
var connector = new builder.ChatConnector({
    appId: process.env.MICROSOFT_APP_ID,
    appPassword: process.env.MICROSOFT_APP_PASSWORD
});
var bot = new builder.UniversalBot(connector);
server.post('/api/messages', connector.listen());

// Create a LUIS dialog and map it's path.
var model = 'https://api.projectoxford.ai/luis/v1/application?id=f01c245d-a368-44a1-ac6c-93d196c58a3a&subscription-key=4cc7be79a2e8489aaca1db350e6fafe1';
var recognizer = new builder.LuisRecognizer(model);
var dialog = new builder.IntentDialog({ recognizers: [recognizer] });
bot.dialog('/', dialog);
{% endhighlight %}

# Configure the LUIS Dialog to Support Triva Questions

{% highlight javascript %}
dialog.matches('Trivia', [
    function (session, args)
    {
        session.send("Astro Orbiter first opened in Tomorrowland in 1974 as Star Jets.");
    }
]);
{% endhighlight %}

# Run the Bot and Test Locally

From a command line in the directory where your bot is located start up the node application.

{% highlight javascript %}
node app.js
{% endhighlight %}

Using the Bot Framework Channel Emulator you can ask your bot a triva question "Tell me about the Astro Orbiter." 

# Conclusion &amp; Next Steps
You know have a bot integrated into a LUIS model! The concepts you learned in this lab are:

1. Configure a LUIS dialog.
2. Connect to the LUIS dialog to a specific intent.

In the [next lab][nextlab] you will configure the bot to use a LUIS dialog to understand the specific question that was asked and respond appropriately. 

<a class="radius button small" href="{{ site.url }}/workshop/dev-days/js-bot/luis-entity/">LUIS Entity</a>

[nextlab]: /workshop/dev-days/js-bot/luis-entity/