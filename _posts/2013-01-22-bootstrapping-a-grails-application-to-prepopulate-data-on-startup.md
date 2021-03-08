---
title: 'Bootstrapping a Grails application to prepopulate data on startup'
date: '2013-01-22T21:15:57+10:00'
permalink: /bootstrapping-a-grails-application-to-prepopulate-data-on-startup
author: 'James Elsey'
category:
    - Grails
    - Groovy
tag:
    - bootstrap
    - grails
    - groovy
---
I’ve been dabbling in a little Grails recently, and I found it quite frustrating having to re-enter some sample data everytime I restarted my application, in order to have the views look meaningful.

Fortunately, theres an easy solution, just do all of your setup in the Bootstrapper class like so :

```groovy
class BootStrap {

def init = { servletContext -&gt;

// Keep references on these as we'll use them for populating sample surveys
Response great = new Response(text: "Great").save()
Response average = new Response(text: "Average").save()
Response poor = new Response(text: "Poor").save()
Response tasty = new Response(text: "Tasty").save()
Response ok = new Response(text: "OK").save()
Response horrible = new Response(text: "Horrible").save()
Response friends = new Response(text: "Friends").save()
Response advert = new Response(text: "Advert").save()
Response radio = new Response(text: "Radio").save()

// These are some other responses that we'll make available in the response bank
["Excellent", "Good", "Satisfactory", "Bad", "Very bad", "true", "false", "Yes", "No", "Maybe"].each {
new Response(text: it).save()
}

Question q1 = new Question(text: "What did you think of the service?", responses: [great, average, poor]).save()
Question q2 = new Question(text: "Was the food nice?", responses: [tasty, ok, horrible]).save()
Question q3 = new Question(text: "How did you hear about our establishment?", responses: [friends, advert, radio]).save()

Survey cosmos = new Survey(title: "COSMOs customer feedback", questions: [q1, q2, q3]).save()
Survey jimmys = new Survey(title: "Jimmys Kitchen customer feedback", questions: [q1, q2, q3]).save()

new User(firstName: 'James',
lastName: 'Elsey',
emailAddress: 'james.elsey.dev@gmail.com',
companyName: 'Jimmys Kitchen',
surveys: [jimmys])
.save()

new User(firstName: 'Manabu',
lastName: 'Takano',
emailAddress: 'manny@hotmail.com',
companyName: 'COSMOs Cardiff',
surveys: [cosmos])
.save()

}
def destroy = {
}
}

```

Then, every time you start the application, you can be sure that it has the above data populated.

Probably not ideal for production apps, but very useful in development