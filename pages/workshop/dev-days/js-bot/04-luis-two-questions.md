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
permalink: /workshop/dev-days/js-bot/luis-two-questions/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will integrate LUIS model into your _Bot_ so your bot can answer specific questions using the entity values the user presented in there question.

# Add Directions Intent

{% highlight javascript %}
dialog.matches('Directions', [
    function (session, args, next) {
        captureState(session, args);

        if (!session.dialogData.entity) {
            builder.Prompts.text(session, 'Which park?');
        } else {
            next();
        }
    },
    function (session, results)
    {
        if (results.response) {
            session.dialogData.entity = result.response;
        }
        
        var location = session.dialogData.entity;

        switch(session.dialogData.entityType) {
            case 'Location::Park':
                session.send('You can go to the %s by following the yellow path.', location);
                break;
            case 'Location::Attraction':
                session.send('You can go to the %s by following the orange path.', location);
                break;
            default:
                session.send('Sorry I don\'t know where %s is located.', location);
                break;
        } 
    }
]);
{% endhighlight %}

# Modify the Trivia Dialog

Modify the Trivia intent from the previous lab so that it uses a common method to capture and store state.

{% highlight javascript %}
dialog.matches('Trivia', [
    function (session, args)
    {
        captureState(session, args);
        
        var response = 'I don\'t know about that yet but I am learning fast.';
        if (session.dialogData.entity)
        {
            var trivia = getTrivia(session.dialogData.entity);
            if (trivia)
            {
                response = trivia;
            }
        } 

        session.send(response);
    }
]);
{% endhighlight %}

Add a function to support the capture and storage of state.

{% highlight javascript %}
function captureState(session, args)
{
    if (args && args.entities.length > 0) {
        var entityRecommendation = args.entities[0];
    }
        
    if (entityRecommendation)
    {
        session.dialogData.entityType = entityRecommendation.type;
        session.dialogData.entity = entityRecommendation.entity;
    }
}
{% endhighlight %}

# Run the Bot and Test Locally

From a command line in the directory where your bot is located start up the node application.

{% highlight javascript %}
node app.js
{% endhighlight %}

Using the Bot Framework Channel Emulator you can ask your bot for directions to the Magic Kingdom. Following that question you can ask the bot, "Tell me about it". Because you are managing state between requests the bot knows you are talking about the Magic Kingdom.

# Conclusion
The concepts you learned in this lab are:

1. Integrate a LUIS model with a chat bot
2. How to use the LUIS response to get specifiy information the user was asking for.
3. Managing state between requests
4. Using state to create a multi-turn conversation