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
permalink: /workshop/dev-days/js-bot/hello-bot/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will create a simple _Bot_ using the Microsoft Bot Framework - think Hello World!

# Create a Folder and Install Dependencies
Create a folder for your bot, cd into it, and run npm init. Follow the promts from "npm init" and accept the defaults

{% highlight javascript %}
npm init
{% endhighlight %}

Get the Bot Builder and Restify modules using npm.

{% highlight javascript %}
npm install --save botbuilder
npm install --save restify
{% endhighlight %}

# Create the Bot in an App.js File

Create an "App.js" file inside your project directory and add the following code.

{% highlight javascript %}
var restify = require('restify');
var builder = require('botbuilder');

//=========================================================
// Bot Setup
//=========================================================

// Setup Restify Server
var server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
   console.log('%s listening to %s', server.name, server.url); 
});
  
// Create chat bot
var connector = new builder.ChatConnector({
    appId: process.env.MICROSOFT_APP_ID,
    appPassword: process.env.MICROSOFT_APP_PASSWORD
});
var bot = new builder.UniversalBot(connector);
server.post('/api/messages', connector.listen());

//=========================================================
// Bots Dialogs
//=========================================================

bot.dialog('/', function (session) {
    session.send("Hello World!");
});
{% endhighlight %}

# Run the Bot and Test Locally

From a command line in the directory where your bot is located start up the node application.

{% highlight javascript %}
node app.js
{% endhighlight %}

Using the Bot Framework Channel Emulator you can ask your bot a question and it should reply with "Hello World!"

# Conclusion &amp; Next Steps
Congratulations! You have created a Hello World bot! In the following labs you will expand the bot so that it can answer sepcific questions and even have a small dialog with the user. The concepts you learned in this lab are:

1. Minimal Restify settup.
2. Configuring a bot program to respond to user request.

In the [next lab][nextlab] you will configure the bot to use a LUIS dialog to respond to user input. 

<a class="radius button small" href="{{ site.url }}/workshop/dev-days/js-bot/luis-question/">LUIS Question</a>

[nextlab]: /workshop/dev-days/js-bot/luis-question/