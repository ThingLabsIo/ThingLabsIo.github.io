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
permalink: /workshop/dev-days/js-bot/luis-entity/
---

# Table of Contents
*  Auto generated table of contents
{:toc}

In this lab you will integrate LUIS model into your _Bot_ so your bot can answer specific questions using the entity values the user presented in there question.

# Modify the Trivia Dialog

Modify the Trivia intent that you created in the previous lab
{% highlight javascript %}
dialog.matches('Trivia', [
    function (session, args)
    {
        if (args && args.entities.length > 0) {
            var entityRecommendation = args.entities[0];
        }
            
        if (entityRecommendation)
        {
            var entity = entityRecommendation.entity;
        }
        
        var response = 'I don\'t know about that yet but I am learning fast.';
        if (entity)
        {
            var trivia = getTrivia(entity);
            if (trivia)
            {
                response = trivia;
            }
        } 

        session.send(response);
    }
]);
{% endhighlight %}

Add a function to support more trivia questions

{% highlight javascript %}
function getTrivia(entity)
{
    var facts = [
        {PartitionKey:"big thunder mountain", Fact:"Big Thunder Mountain took 10 years of planning and 18 months of construction to complete. The construction used 650 tons of steel, 4,675 tons of specially-formulated 'mud,' and more than 9,000 gallons of paint. The cost of $17 million equals that of the California Disneyland opening in 1955. Additionally, $300,000 was spent on authentic set decorations."},
        {PartitionKey:"enchanted tiki room", Fact:"Originally known as the Tropical Serenade,â€ the Tiki Room in Adventureland was once sponsored by Florida Citrus Growers."},
        {PartitionKey:"hall of presidents", Fact:"The Hall of Presidents had its origins as an audio-animatronic exhibition called Great Moments with Mr. Lincoln, which premiered at the 1964-65 New York World's Fair."},
        {PartitionKey:"its a small world", Fact:"Both the Carousel of Progress and It's A Small World made their debut at the 1964-65 New York World's Fair"},
        {PartitionKey:"magic kingdom", Fact:"Magic Kingdom opened as the first part of Walt Disney's planned Florida Project on October 1, 1971. It was the only theme park on the resort at the time and opened concurrently with two hotels on the property: Disney's Contemporary Resort and Disney's Polynesian Resort. The park opened with 23 attractions, three unique to the park and 20 copies of attractions at Disneyland."},
        {PartitionKey:"pirates of the caribbean", Fact:"Pirates of the Caribbean did not open until 1973, 2 years after MK opened..originally Imagineers thought Pirates would not be popular in Florida due to its proximity to the Caribbean, but Guests demanded it, and it quickly became the most popular ride at the park"},
        {PartitionKey:"splash mountain", Fact:"Splash Mountain in Adventureland features a five-story, free-fall plunge at a 45-degree angle into a splash pool at a speed of 40 miles per hour."},
        {PartitionKey:"space mountain", Fact:"Space Mountain was the first ride to debut at Disney World before coming to Disneyland"},
        {PartitionKey:"swiss family treehouse", Fact:"The 60-foot-tall Swiss Family Treehouse in Adventureland weighs approximately 200 tons and is made of concrete and thousands of polyethylene leaves."},
        {PartitionKey:"the haunted mansion", Fact:"The Haunted Mansion uses state-of-the-art Omnimover vehicles called Doom Buggies."},
        {PartitionKey:"tommorowland", Fact:"Astro Orbiter first opened in Tomorrowland in 1974 as Star Jets."},
        {PartitionKey:"tomorrowland transit authority", Fact:"The Tomorrowland Transit Authority was originally called the WEDway People Mover (WED standing for Walter Elias Disney)."},
    ];
    
    for (var i = 0; i < 10; i++)
    {
        if (facts[i].PartitionKey === entity)
        {
            return facts[i].Fact;
        }
    }
}
{% endhighlight %}

# Run the Bot and Test Locally

From a command line in the directory where your bot is located start up the node application.

{% highlight javascript %}
node app.js
{% endhighlight %}

Using the Bot Framework Channel Emulator you can ask your bot a triva question "Tell me about the Magic Kingdom." You can also ask about Splash Mountain, Tomorrowland, Space Mountain and so on.

# Conclusion &amp; Next Steps
You know have a bot integrated into a LUIS model! The concepts you learned in this lab are:

1. How to use the LUIS response to get specifiy information the user was asking for.

In the [next lab][nextlab] you will configure the bot to use a LUIS dialog to understand the specific question that was asked and respond appropriately. 

<a class="radius button small" href="{{ site.url }}/workshop/dev-days/js-bot/luis-two-questions/">LUIS Two Questions</a>

[nextlab]: /workshop/dev-days/js-bot/luis-two-questions/