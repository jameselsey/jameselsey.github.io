---
title: 'Getting to grips with Grails, building a survey management system'
date: '2014-01-25T12:30:59+10:00'
permalink: /getting-to-grips-with-grails-building-a-survey-management-system
author: 'James Elsey'
category:
    - Grails
    - Groovy
tag:
    - api
    - cloudbees
    - grails
    - groovy
    - prototype
    - qr
    - startup
    - survey
---
Sometime in late 2012 I was discussing dissertation project ideas with my girlfriend, as she was coming up to her final year of a computing bachelors. The usual option chosen by many graduates would be to just build a website or an app, or do some form of market research. We decided to encompass all 3 to produce something that works, but ultimately something that could be of value. If I had the time, energy, and funds I’d pursue this as it has potential for a startup, but I don’t, so the important thing that I’ve taken away is the experience working with groovy, grails, and android.

The Idea…
=========

There are 2 main business drivers behind this project. Firstly we wanted to provide a service whereby restaurant owners can register, create surveys, and make them accessible to staff such as printing QR codes onto the back of their menus. Secondly, we wanted to approach this from the end users point of view, whereby customers sitting in the restaurant could download an app for free off the public market places, scan the said QR code, and be presented with the survey the restaurant owner had created. They would fill it in via the app and submit, the restaurant owner then has immediate access to the results in the form of statistics and graphs.

![Landing page for Roosearch](/assets/post_images/2013/Screen-Shot-2013-12-19-at-18.50.24.png)

The outcomes that we’re after:

- Better visibility to restaurant owners on how their customers feel
- Easy and seamless access to surveys for the customers
- A scalable application which can handle increasing users as demand grows
- A platform for advertising new products and features

There are 3 components to this solution:

1. Grails web application
2. Rest API (built into the grails application)
3. Android app

Why Grails?

- Develop in groovy, so very accessible to java developers.
- Quick to prototype with “convention over configuration”
- Views auto generated if using scaffolding.
- Easily deployable into the cloud, package as a war and deploy to cloudbees

The Prototype…
==============

If you’re starting out with grails, I’d highly recommend that you get a copy of [IntelliJ](www.jetbrains.com/idea/) ultimate edition (and a copy of [Grails in action](https://www.google.co.uk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=4&cad=rja&ved=0CE0QFjAD&url=http%3A%2F%2Fwww.amazon.co.uk%2FGrails-Action-Glen-Smith%2Fdp%2F1933988932&ei=nQCzUv-wJKKj0QWDyoD4DQ&usg=AFQjCNGWajpyMSR8hwoyKU2AS0f1pz1wCw&sig2=oZyWN4b2FNJQPjawMmnjEw&bvm=bv.58187178,d.d2k)), the support for grails is fantastic and I found it far easier than using eclipse. Whilst there are some excellent tutorials on grails out there (the official documentation is also very good) I’ll hold off and just jump right into how the application works.

One of the awesome features of grails is that it follows the “convention over configuration”, which simply means that if you follow the convention implied by the framework, you don’t have to be concerned about configuration. You can’t escape configuration entirely, but boilerplate plumbing can be inferred by convention. An example of that is if you name your controllers like “SurveyController”, grails automatically knows its a controller for the survey class, based on naming conventions. A similar convention applies for views.

#### **Domain model**

![Roosearch entity relationship diagram](/assets/post_images/2013/Screen-Shot-2013-12-19-at-19.06.55.png)

Our data model is quite simple. We have a user, the user has some surveys, each survey has a number of questions, and each questions has a number of predefined responses. The domain classes are self explanatory, but it’s probably worth mentioning a few tweaks I made.

```groovy
class User {
    String firstName
    String lastName
    String emailAddress
    String companyName
    String facebookPageLink
    String twitterHandle

    static hasMany = [surveys: Survey]
    static constraints = {
        facebookPageLink nullable: true
        twitterHandle nullable: true
    }
}
```

By default, all fields are mandatory, however in the above example of the User class we can override these constraints to set them as nullable. There are various other constraints that you can set, have a look at the documentation.

```groovy
class Survey {

    Integer id
    String title

    static hasMany = [questions: Question]

    static mapping = {
        questions lazy: false
    }

    static constraints = {
        id()
        title()
        questions()
    }

    String toString(){
        return title
    }
}
```

The relationships between the classes are defined by the “static hasMany”. This basically says that one Survey has relationships to many Questions, and this relationship is identified by “questions”.

The mapping block instructs the questions to be eagerly loaded, so once a survey is loaded into memory, so are all of its questions, opposed to just the Ids which would then be loaded lazily.

It’s also useful to override the toString method on your domain objects, particularly if you have relationships as the scaffolding will create drop down lists in your views. If you don’t override toString with something sensible, you’ll just see the object hash codes instead, which isn’t very useful to the user.

#### Controllers

It’s the responsibility of the controllers to manipulate the underlying data model (via services for example), and respond with views to the user. You can read more about the MVC pattern [here](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller).

To get started, you could simply enable scaffolding like so.

```groovy
class LoginController {

    static scaffold = true

    def index = {
        render(view: "login.gsp")
    }
}
```

Scaffolding is an excellent feature of grails to get you started. Grails knows the structure of your domain object, therefore it is able to dynamically create controller CRUD operations, and views to manipulate your objects. That one small line of code and you can create, updated, delete, and view your objects! Fantastic eh?!

The bad news…

Whilst [scaffolding](http://grails.org/doc/latest/guide/scaffolding.html%E2%80%8E) is great to get you started, the moment you want to do something out of the ordinary, or customisation on views, scaffolding becomes a bit useless, and you’ll have to implement your own controllers (and possibly views). Fortunately, grails is quite flexible so you can leave scaffolding on and just override the methods that you want to customise. As with views, you’re best off getting grails to generate them for you and then customise them, to save you having to write the entire controller/view from scratch.

The methods you can override, and there general uses are:

- index – default action, usually just redirects to list
- list – list all of the objects, handle pagination, filtering etc
- create – render view to create new object
- save – handle creation of new object, validation etc.
- edit – render view to edit an object
- update – handle update of object
- delete – delete an object

You can read more about the controller actions on the [grails documentation](http://grails.org/doc/latest/guide/theWebLayer.html%E2%80%8E).

You can see in the show() method on the SurveyController that I’ve customised it to to add some charts into the response model. You can see how I generate the chart data by looking at the source code in github. The view can then render these as javascript charts (which I’ll come onto in a moment)

```groovy
    def show(Long id) {
        def surveyInstance = Survey.get(id)
        if (!surveyInstance) {
            flash.message = message(code: 'default.not.found.message', args: [message(code: 'survey.label', default: 'Survey'), id])
            redirect(action: "list")
            return
        }

        def charts = getCharts(surveyInstance)

        [surveyInstance: surveyInstance, charts: charts]
    }
```

#### Views

Being quite fond of the default views that grails generates, and not wanting to invest a great deal of time with customisation for this prototype, I chose to generate the views and then just tweak as I needed. In reality, the only customisation I needed to do was to place a “generate QR code” link, and to insert some javacript charts for displaying survey statistics.

Having assessed [HighCharts](www.highcharts.com/%E2%80%8E), [D3](http://d3js.org), and the [Google visualisation API](https://developers.google.com/chart/interactive/docs/reference), I opted for the latter as I felt it was far simpler to use and I didn’t have any need for the advanced features that HighCharts and D3 come with, and there was a plugin for gvisualisation.

Displaying charts was straightforward, after installing the visualisation plugin, add this snippet of code to iterate over the charts that were added to the model and display a barCoreChart.

```xml
<g:each in="${charts.values()}" var="item">
            <gvisualization:barCoreChart
                    elementId="chart-${item.question_id}"
                    title="${item.question}"
                    width="${400}" height="${240}"
                    columns="${item.column}"
                    data="${item.data}"/>

            <div id="chart-${item.question_id}" align="center"></div>
        </g:each>
```

This would then display something like the following, you can change various elements of the charts such as the chart type, axis labels, sizes and titles, please refer to the documentation.

![Charts using Google Visualisation](/assets/post_images/2013/Screen-Shot-2013-12-19-at-18.52.58.png)

#### QR codes

QR codes make it incredibly easy to share data to android devices, my intention was to embed a user ID in a QR code, when scanned the app can request all surveys pertinent to the user ID.

Generating QR codes is easy with the [qrcode plugin](http://grails.org/plugin/qrcode). I have provided a link on the users view to generate a QR code:

```xml
<span class="property-value" aria-labelledby="qr-label">
                <g:link controller="user" action="generateQrCode" id="${userInstance.id}">Generate QR Code</g:link>
            </span>
```

This is bound to the generateQrCode action on the user controller, which will create a QR code from a user id and display it

```groovy
    def generateQrCode(Long id){
        println "Generate QR code here..."
        String data = "$id"
        int qrSize = 500

        QRCodeRenderer qrcodeRenderer = new QRCodeRenderer()
        qrcodeRenderer.renderPng(data, qrSize, response.outputStream)
    }
```

As you can see, it is as simple as providing the data to be encoded, the size (x==y), and the output stream, in this case the response. When you click the link, you should see the following:

![QR code generated by the qrcode plugin](/assets/post_images/2013/Screen-Shot-2013-12-19-at-18.53.33.png)

#### API

The website element is designed for the restaurant owners, the end users will be using an android app to complete surveys. Whilst I could have developed a mobile responsive page, I felt that an android app would bring a better overall experience to the user.

I have created a controller, ApiController that enables users to request surveys, and post responses.

Firstly, I created the URL mappings for this new controller

```groovy
	static mappings = {
        "/api/customer/$customerid"(controller: "api", action: 'getCustomer')
        "/api/survey/$surveyid"(controller: "api", action: [GET: 'getSurvey'])
        "/api/survey"(controller: "api", action: [POST: 'surveyComplete'])

        "/$controller/$action?/$id?"{
            constraints {
                // apply constraints here
            }
        }

		"/"(controller: "home")
		"500"(view:'/error')
	}
```

Requests on /api/customer/$customerid, such as /api/customer/123 are routed to the getCustomer method on the api controller. The same is true for the second mapping, however the action is a GET on getSurvey (in hindsight, the first mapping should be restricted to the GET method too). The third mapping is a POST on /api/survey which will be invoked when the user has completed a survey on their device.

```groovy
    def getCustomer(){
        User u = User.get(params.customerid)

        def surveysToPresent = [:]

        u.surveys.each {
            surveysToPresent << [title: it.title, id: it.id]
        }
        render(contentType: 'text/json') {[
                'company_name': u.companyName,
                'twitter' : u.twitterHandle,
                'facebook' : u.facebookPageLink,
                'surveys' : [surveysToPresent]
        ]} as JSON
    }
```

The getCustomer method finds the user from the customerid on the request path, retrieves the surveys and transforms them to a map containing the title and id (we don’t need the entire survey object when the user is presented with a list of surveys to select). The render statement enables us to return a json response very easily, we just return a map and grails (jackson) takes care of the json marshalling.

```groovy
    def getSurvey(){
        Survey s = Survey.get(params.surveyid)

        def questionsToPresent = [:]

        questionsToPresent = s.questions.collect {
            [
                    id: it.id,
                    text: it.text,
                    responses : it.responses.collect{ resp ->
                        [id: resp.id, text: resp.text]
                    }
            ]
        }

        render(contentType: 'text/json') {[
                'id' : s.id,
                'title': s.title,
                'questions' : questionsToPresent
        ]} as JSON
    }
```

The getSurvey method behaves in a similar manner to getCustomer, it builds a map and renders as json.

```groovy
    def surveyComplete(){
        def jsonObject = request.JSON

        Survey theSurvey = Survey.findById(jsonObject.id)

        jsonObject.responses.each{ response ->
            theSurvey.questions.find {it.id == response.question_id}.responses.find {it.id == response.response_id}.numberOfPeopleSelected++
        }
        theSurvey.save(flush: true, failOnError: true)

        render(status: 204)
    }
```

The surveyComplete will retrieve a survey by id, find the responses the user has provided, and increment a count. The survey is then saved and a “204 No Content” is returned.

I’ll cover how the android app consumes these services in my next post.

#### Deployment

As this project is just a prototype, I decided to host it on a free Cloudbees instance. The application doesn’t have any persistence layer, and all data is held in memory (which is fine for its current purpose), so when Cloudbees hibernates the instance after a period of inactivity, all user data will be lost. Deploying is simple, build the war using

```
grails war
```

Then upload the war file from the target directory to your cloud bees account, or use the command line [cloud bees SDK](http://wiki.cloudbees.com/bin/view/RUN/BeesSDK).


#### [View source code on Github](https://github.com/jameselsey/roosearch-web)

#### [View live demo](http://roosearchdev.jameselsey.cloudbees.net/)

*(if the live demo link doesn’t work, try again in 10 minutes as the instance will be waking from hibernation)*

#### Resources

- [Grails website](http://grails.org)
- [Grails in action book](https://www.google.co.uk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=4&cad=rja&ved=0CE0QFjAD&url=http%3A%2F%2Fwww.amazon.co.uk%2FGrails-Action-Glen-Smith%2Fdp%2F1933988932&ei=nQCzUv-wJKKj0QWDyoD4DQ&usg=AFQjCNGWajpyMSR8hwoyKU2AS0f1pz1wCw&sig2=oZyWN4b2FNJQPjawMmnjEw&bvm=bv.58187178,d.d2k)
- [Cloudbees SDK](http://wiki.cloudbees.com/bin/view/RUN/BeesSDK)
- [Google visualisation grails plugin](http://grails.org/plugin/google-visualization%E2%80%8E)
- [Grails qr code plugin](http://grails.org/plugin/qrcode)
- [MVC pattern](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)
- [D3](d3js.org/%E2%80%8E)
- [HighCharts](www.highcharts.com/%E2%80%8E)