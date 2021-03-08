---
title: 'An intro to Node.js, building a URL shortener'
date: '2013-12-15T20:25:35+10:00'
permalink: /an-intro-to-node-js-building-a-url-shortener
author: 'James Elsey'
category:
    - Javascript
    - Node.js
tag:
    - cloudbees
    - javascript
    - node
    - nodejs
    - url
    - web
---
Node has been on my todo list of things to investigate for a little while now, whilst I don’t have much background in javascript, after constantly hearing about it when I was working at O2 Telefonica, I thought I better see what the fuss is all about.

![](http://cwbuecheler.com/web/tutorials/2013/node-express-mongo/nodelogo.png) To quote the Node website: “Node.js is a platform built on Chrome’s JavaScript runtime for easily building fast, scalable network applications. Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient, perfect for data-intensive real-time applications that run across distributed devices.”

The best way to learn how to use a new technology is to try and build something with it. I’ve chosen to build a URL shortener because its a relatively straightforward concept; you take a URL and store it against a key, then when that key is requested, you redirect to the original URL. These are commonly used on twitter and other social media where you are limited to sharing small messages, as it enables you to have short URLs (normally on short domains such as [bit.ly](https://bitly.com/)) refer to a longer URL that you want to share.

I’ve decided to use [express](http://expressjs.com/), which is a framework for node js for building web applications, I’ve also chosen to use [Jade](http://jade-lang.com/) for my views, which is a node template engine for generating HTML views.

Lets get started.

Firstly, you’ll need to setup a development environment if you haven’t already got one. I’m using [IntelliJ IDEA](http://www.jetbrains.com/idea/) and am fortunate that there is node js support that will help you create a new project.

If you’re not using IDEA, or want to create a node project yourself, you’ll need to create a packages.json file, delcare your dependencies, and run npm install, then you can create the app.js and start developing. You can find more info on how to do that on the [express documentation](http://expressjs.com/guide.html).

Creating the short URL
----------------------

When the user first lands on the site they are present with a form where they can shorten a URL. As we can see from this line in the main.js file the index route is displayed for requests on the base URL:

```javascript

app.get('/', routes.index);

```

This refers to the index.js file in the routes directory, which in turn renders a view back to the client, by doing the following:

```javascript
exports.index = function (req, res) {
 console.log('Displaying index page where users can enter a long url')

res.render('index');
};

```

Node automatically knows that it needs to render a jade view, because we told it earlier where the views are located, and what view engine to use when we declared these in our main.js:

```javascript

app.set('views', path.join(__dirname, 'views'));
 app.set('view engine', 'jade');

```

A quick peak at our index jade file and we can see that we have a very basic form that accepts a single text field and posts this to the /create endpoint

```
extends layout

block content
 h1= title
 h1.text-center Node.js URL Shortener

div.container
 div.content
 div.well
 form.form-horizontal(name="input", action="/create", method="post")
 div.form-group(align='center')
 p
 input(type="text", name="urlToShorten", class="form-control", placeholder="Enter a URL")
 p
 input(type="submit", value="Shorten", class="btn btn-primary")

```

Diving back to our main js file, we can see that requests (specifically POST requests) are handled by the createShort function in the shorty.js file:

```javascript
app.post('/create', shorty.createShort);

```

This is where the magic happens. Firstly, we need to get hold of the URL that the user submitted in the form, that is relatively easy as we can get it directly off the request body, as shown in the below code snippet. Next we do a quick check to see if its null and display a message to the user if they didn’t provide a URL. If they have provided a URL, we check to see if we need to prepend it with http://., then we create a shortcode for that URL and render a page that will display the link for the user to share.

```javascript
/*
 * POST creates a short url from a long url
 */
 exports.createShort = function (req, res) {

var urlToShorten = req.body.urlToShorten;
 if (!urlToShorten) {
 console.log('Request did not contain a url to shorten, please provide urlToShorten');
 res.render('short', {message: 'Request did not contain a url to shorten, please provide urlToShorten'});
 } else {

console.log("Request to shorten " + urlToShorten);

urlToShorten = addhttp(urlToShorten);
 var baseUrl = 'http://' + req.app.get('hostname') + '/';

var shortCode = createShortCode(urlToShorten);
 res.setHeader('Content-Type', 'text/html');
 res.statusCode = 200;
 res.render('short', { shortUrl: baseUrl + shortCode });
 }
 };

```

The reason for prepending the protocol onto the link, is because this is required for the Location header on a redirect which we will serve when the short URL is requested, as mentioned in the [HTTP RFC spec](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.30), this needs to be an absolute URI. This is a bit of a dirty hack and I’m not that happy with it. Ideally, I would like to create a URL object with the string the user has supplied, regardless of what prefix it has, I was hoping that the node [url functions](http://nodejs.org/api/url.html) would help with that, but it seems that when parsing and then formatting the URL, it doesn’t retain the protocol. It is also entirely possible that I’m not using the API correctly, perhaps someone can comment, or suggest a better mechanism for this.

To create the short code, we generate a random alphanumeric string of 5 characters, this is quite a simple function. We then add the short code and URL to a collection so we can look them up when the user requests the short code.

Redirecting to the original URL
-------------------------------

Looking back at the main js file, we can see another route. This is used when a user requests their short url.

```javascript
app.get('/:short', shorty.getLong);

```

Firstly, we grab the short code from the request path. We have to strip the first character as that will be a slash, such as “/ABC123”. Then we find the long URL that the short code refers to by looking in the collection where we stored it earlier. After that, its just a case of writing a 302 redirect response with the Location header set to the long URL, thats it really.

```javascript

/*
 * GET retrieves long url from short url
 */
 exports.getLong = function (req, res) {

// grab the path and strip the leading slash
 var shortCode = req.path.substring(1);

console.log("Fetching URL indexed by " + shortCode);
 var theLongUrl = shortToLong[shortCode];

console.log('Short code ' + shortCode + " refers to " + theLongUrl);

console.log("redirecting to " + theLongUrl);
 res.writeHead(302, {'Location': theLongUrl});
 res.end();
 };

```

Conclusion
----------

Whilst I don’t have much of a background in front end web development, my javascript skills are pretty basic, saying that, node js is quite intuitive and the documentation is good enough to get you through the basics. Node has quite a small learning curve (at least for the basics), coupled with StackOverflow for reference, you can build web apps relatively quickly and easily.

Would I use Node again? Absolutely, however I think it’d be far better suited to projects requiring a substantial number of concurrent users, such as web chat applications, or for gaming backends. For building web applications I’d probably choose [Grails](http://grails.org/) as I’m more familiar with Java and Groovy, plus you get all the benefits of [Spring](http://spring.io/). Scaffolding is also great.

Node would be a great choice for building development stubs, I’ve built several mobile apps that require backends, for testing, you want a stubbed backend that returns canned responses. You can do this incredibly easy in node by using returning [static](https://github.com/cloudhead/node-static) files served up as JSON responses, plus its very lightweight so you can integrate into your CI nicely.

You can see a live demo of this on my cloudbees account [here](http://nodeshort.jameselsey.cloudbees.net/).

Resources
---------

- [Express JS documentation](http://expressjs.com/guide.html)
- [Jade tutorial](http://jade-lang.com/tutorial/)
- [HTML to Jade online free converter](http://html2jade.aaron-powell.com/)
- [Live demo](http://nodeshort.jameselsey.cloudbees.net/) (if it doesn’t load first time, try again in 10 minutes as the instance will be waking from hibernation)
- [Github sourcecode](https://github.com/jameselsey/nodejs-url-shortener)