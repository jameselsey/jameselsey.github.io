---
title: "How to setup Google Analytics on your Jekyll Github Pages site"
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - jekyll
  - wordpress
  - meta
  - google-analytics
---

Having setup Google Analytics on my website and Android apps many moons ago, I was surprised how easy it was to setup on Jekyll, there was no messing around with libraries or scripts, just config!

There's a few posts out there saying you need to save a script to an html, then include that in a layout, nope, you don't need to do that.

Just add this snippet to the end of your `_config.yml` file

```yaml
analytics:
  provider: "google-gtag"
  google:
    tracking_id: "G-D69JW2T64K"
    anonymize_ip: false # default  
```

Obviously replacing the `tracking_id` with your own, don't worry that it doesn't start with `UA-` like many of the tutorials out there suggest.

To find your tracking ID, or measurement ID as it may be called, log in to Google Analytics, then via the Admin menu, find your account/stream, and you should be able to see the "Web Stream Details" like so.

![Google Analytics](/assets/post_images/google-analytics.png)

Depending on when you're reading this, the UI may change, but you just need to find that measurement ID and place into the yaml above.

That's it!
