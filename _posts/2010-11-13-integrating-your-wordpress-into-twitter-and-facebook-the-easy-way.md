---
title: 'Integrating your wordpress into twitter and facebook, the easy way'
date: '2010-11-13T14:03:45+10:00'
permalink: /integrating-your-wordpress-into-twitter-and-facebook-the-easy-way
author: 'James Elsey'
category:
    - Wordpress
tag: []
---
I’ve been searching for a viable and easy solution for integrating my wordpress posts into facebook and twitter for some time now, and have finally found an easy solution, it takes about all of 10 minutes to setup. Twitter is essentially the “integration hub” in this process

**What I’m trying to achieve** : When I post a new blog post onto my wordpress site, I want it to appear in a tweet on my twitter account (with a link), and then to also appear on my status/wall on my facebook account.

**What you need to do :**

*Connect facebook to twitter*

1. Login to your facebook account on facebook.com
2. Search for the twitter app, install it and grant it access
3. Make sure you select the option to post tweets to status/wall
4. To prove this step worked, login to your twitter account and tweet something, then check facebook, it should appear on your wall / status page.

*Connect wordpress to twitter*

1. Firstly download the twitter tools plugin for wordpress, I had a difficult time finding it via the wordpress plugin control panel, so it may be easier to just download the zip
2. Login to your wordpress blog, navigate to the plugins control panel and upload the twitter-tools.zip
3. Follow the configuration instructions that the plugin provides (its really simple, just obtain some keys from the links it provides)
4. Once the plugin is connected using the API keys, go to the Settings control panel in your wordpress, and configure how you want the tweets to display. I chose to only allow one way integration, meaning wordpress to twitter, and not the other way around ie twitter tweets becoming wordpress posts.
5. Finally, if you go back into your Plugins contorl panel, you will notice other twitter tools plugins that came bundled, you can activate these for additional functionality. I would recommend setting up the bit.ly integration for shorter URL links, as tweets are restricted to a short length this will allow you to have URLs for larger blog titles
6. Finally, give it a spin! Create a new wordpress post, publish it, and check it first appears on twitter, and then facebook pulls it through

Good luck!