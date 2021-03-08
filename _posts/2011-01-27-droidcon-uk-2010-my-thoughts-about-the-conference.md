---
title: 'DroidCon UK 2010; My thoughts about the conference'
date: '2011-01-27T20:08:08+10:00'
permalink: /droidcon-uk-2010-my-thoughts-about-the-conference
author: 'James Elsey'
category:
    - Android
    - 'Conferences and Meetups'
tag:
    - android
    - conference
    - droidcon
    - java
    - london
    - uk

---
![droidcon](/assets/post_images/2011/droidcon.jpg)

Over summer 2010, my company held an in-house competition to muster up some interest in mobile  
development, by providing a prize for anyone who could come up with an innovative mobile  
application, platform independent. After this competition, fuelled by fascination for all things  
Android, I requested to attend DroidCon UK, a conference dedicated to Android development.

Excited by the line-up of speakers, talks from Google, and celebrities of the Android world such  
as Mark Murphy, this was a fantastic opportunity to go along and soak up as much knowledge as  
possible, and see what people are achieving with the latest mobile technology.

- - - - - -

Day 1 consisted of a Bar Camp style approach. At the beginning of the day all of the attendees and  
speakers were gathered into the main hall, where people could suggest topics of discussion for the  
day. These were quickly sorted into a timetable and as an attendee you were free to hop between  
talks and participate in discussions around the areas you were interested in. I attended the following  
talks in day 1

**Creating responsive UIs**  
*Erik Hellman, lead architect @ Sony Ericsson*  
**Synopsis** : This talk was aimed at detailing some of the key principles around user interface design,  
the key note being “Remove unwanted processing from the primary thread”. This equated to  
enforcing that any slower processing should be moved off the main thread, otherwise the UI  
responsiveness would be reflected. Also it was pointed out that a major reason for applications  
failing to impress the user is by not responding within 1000ms, often due to longer execution tasks  
operating on the main thread. There were also some discussions around OpenGL and “skinning”  
images onto 3D objects and how these can be achieved

**Location based services**  
*Nick Black, founder @ CloudMade*  
**Synopsis** : Location based services are one of the key features that mobile development has over  
desktop development, providing the mobile user information about where they are located, or using  
this for other purposes. This session was primarily a brainstorming session regarding how LBS can  
be improved, one of the major drawbacks being that an internet connection is required in order  
to use such facilities, however CloudMade have created an application that vectors all maps into a  
single download, making some map functionality available offline. There was also discussion of GPS  
connectivity, such as only updating your location on a coarser scale on long distance journeys to save  
battery, and producing finer scale GPS when you come closer to your destination, at a street level.  
There was also discussion about the future of LBS within android, such as the embedding of Android  
devices into lease / rental vehicles, and also to private vehicles for use in conjunction with Pay as you  
Go Insurance schemes.

**Google Bootcamp**  
*Google Inc representatives*  
This was a presentation put on by 4 representatives from Google USA, which was a question and  
answer session. There was quite a lot of talk regarding the NDK, which I believe is the Native SDK for  
Android, used for more low-level operations such as “flashing ROMs”.  
Several questions were directed at the latest release of Android, code named Gingerbread  
and due for release anytime soon, unfortunately Google were unable to comment on anything  
regarding Gingerbread, or even confirmation of the existence of such release, contrary to a 10 foot  
Gingerbread statue recently being raised on the roof of a Google office in the USA…  
Other topics ranged from HTML5 support on the imminent Google TV, and the support for new  
codecs, file formats, and custom formats in further releases

**Freeing your Intents**  
*OpenIntents.org*  
The OpenIntents organisation was established to help the Android community by publishing intents  
that applications expose, allowing developers and consumers to access and share functionality that  
applications provide. For example there are hundreds of weather applications available, so why  
create a new one when you can use an existing intent from one of those applications? This seminar  
was dedicated to discussing how we as developers can contribute to the greater good of the Android  
community.

**The Android Market**  
*Mark Murphy, founder @ CommonsWare*  
This was one of the talks I was looking forward to, Mark is certainly one of the rock stars of the  
Android world, with various books, applications and other endeavours, Mark clearly knows his stuff,  
and I was itching to soak up some of that knowledge.  
This talk was geared towards the publication of your applications, and some of the drawbacks of  
the current Android market. Some of the key issues regarding the official Android market is that  
searching for apps is not always easy, often returning incorrect results. Another key issue is the  
rating system, it is easy for people to download your app, then give it poor rating out of spite.  
There is no way for the application developer to comment back on these, so applications can be  
downrated and given bad reputation quite easily from a few downloaders.  
Another issue was the existence of multiple markets, with the Apple products there is just one  
central point of contact for applications. With Android there is the official Google marketplace, but  
there are also other ones such as the Orange application store and various others. Mark identified  
some of the drawbacks of these such as having to publish to multiple markets, which could become  
a considerable task when maintaining multiple applications. Mark also tried to generate interest  
around developing an open source, community driven market place for Android applications.

**Continuous Integration**  
*Hugo Josefson, Lead developer @ JayWay*  
This was by far one of the best talks over the course of the two days, and without a doubt one of  
the most useful practices to understand. The talk consisted of a walkthrough of the maven-android  
plug-in, which allows developers to automatically build their android APK files, and how automated  
tests can be integrated into this procedure. There was also a brief discussion on how Hudson can  
also be brought into this, to allow one continuous flow of automated building, deployment, and  
testing. I was particularly interested in how the Hudson build server can deploy android applications  
to various headless emulators for testing, this would enable you do test it on various configurations  
such as 2.1 devices, 1.6 devices, low res devices, higher res devices etc. This is most certainly an area  
that requires further investigation.

**Orange B2B**  
*Orange Representatives*  
This was a brief talk by part of the Orange sales and marketing team on how Orange are embracing  
this exciting new platform and investing a lot into various areas such as their own app market place.

- - - - - -

Day 2 consisted of similar talks, but in a more structured format. Kicking off the day was a  
presentation by the chief technology office from Reuters, giving a demonstration about how  
important mobile devices are for journalism. He explained how android embedded devices will be  
enabling journalists to capture information on laptops, phones, and cameras, and have all of these  
sync directly into their news systems.

Next there was a talk from one of the vice presidents at Orange, explaining how seriously committed  
Orange are to android, and how they are investing heavily in their partner-connect business, and  
their own application marketplace.

Following the opening talks of the day, the next session was with Sean Owen, one of the authors  
of the infamous “barcode scanner” application that is consistently amongst the top downloads in  
the android market place. One of the interesting points of this conference was the sheer amount  
of barcodes, people were enthusiastic about there barcodes. Their barcodes were on presentation  
slides, banners, business cards, even t-shirts. Of course this app makes it all possible being able to  
photograph the barcode and interpret it. One of the keynotes of this speech was to make your app  
a dependency in other apps, ie make other people rely on your apps features for their own usage,  
it will drive downloads for your application as the end user will require your app for the underlying  
functionality.

The next talk was dedicated to JRuby on android, I must admit that this wasn’t the best talk of the  
event. There was a lot of emphasis on how JRuby is quick for prototyping applications, but it appears  
that the overhead involved in using JRuby on android is considerably larger than just using plain Java  
on android. Android development is reasonably straightforward, so I failed to see any benefits of  
using JRuby, but I attended the talk to be proved wrong.

Following on from JRuby, there was another presentation by Mark Murphy, regarding “Java and re-  
use” in android. This talk was geared towards the re-usage of code when developing in android, such  
as creating library projects, which can then become dependencies in other projects. For example if  
you have a whole suite of applications that have common functionality, this could easily be built into  
a library project, which could then become a dependency in your applications, thus promoting the  
re-use of common code.

- - - - - -

The next talk was beneficial, some best practices by Google, in which the following sins and saints  
were discussed  
**Sins**

- *Responsiveness*  
  You must make your application responsive, if there is lots of pauses or slow user interaction, the  
  user will quickly get bored and give up on your application, possibly looking for alternatives. The user  
  experience must flow well, and avoid using loading screens if at all necessary. It is also suggested to  
  render the main view with empty containers, and fill in content as it becomes available, rather than  
  displaying nothing for some time, then loading all the data at once.
- *Threading*  
  Make intelligent use of threads and async tasks to keep unwanted information away from the main  
  UI thread, such as exception handling
- *Resources*  
  Don’t use too many resources, use only those which you must. Don’t overuse wake locks or update  
  GPS locations unnecessary, it consumes battery and processing power, potentially affecting the  
  responsiveness of your applications
- *Hostility*  
  Don’t make your application hostile, make it friendly and welcoming to the user, remember that  
  the user is your #1 priority, after all they are the one paying you for your application. Make generic  
  buttons do the same thing as other applications, or how the user would expect them. For example  
  don’t override the menu/back buttons and provide some fancy feature. Use the menu button for  
  displaying a menu, and the back button for going back Also don’t remove the task bar, people may  
  want to multitask whilst using your application.
- *Arrogance*  
  Dont use undocumented APIs, make apps behave consistently. Support landscape and portrait  
  mode, don’t assume all users have a portrait phone, some may have a landscape Google TV.  
  Don’t disable rotation handling and respect the android application lifecycle model. Design your  
  application for every handset in mind, don’t remove yourself from the wider market for targeting a  
  single piece of hardware.

**Saints**

- *User Interface*  
  Make the application look good, use layout hierarchies, hiring a graphic designer will go a long way
- *Generosity*  
  Use intents to make your application open, encourage people to use your application as a building  
  block for theirs.
- *Ubiquity*  
  Leverage the hardware
- *Utility*  
  Solve problems, create an app that identifies and solves a particular problem
- *Epic(ness)*  
  Don’t ever be just satisfied with “good”, always aim for “perfect”

The last talk of the day was geared towards the sheer volume of android devices being produced on  
the market and how this will affect the industry in the future. It has been estimated from market  
research that there will be 500 million tablets shipped by 2015, not including eReaders.  
32% of these are android devices, if your app is sold to just 0.1% you could be targeting 160,000  
users. An app of £0.69 would generate a potential £110,000 of revenue, indicating the potential of  
the market.  
There will be new devices, such as the Google TV which will run android and use HTML5, apps will be  
targeted towards that platform also.

It was absolutely clear to see, that the big names in the telecommunications world such as Orange  
and Vodafone, are taking the Android platform very seriously, and with a presentation by Christophe  
François from Orange it was evident that they are investing a considerable amount of funds into  
marketing and development. Having someone of Vice President level speaking at such an event  
further displays their commitment to this exciting new technology.

Overall, I would say by attending DroidCon I’ve learned a lot about the platform and what it has in  
store for the future. I was expecting the talks to be difficult to comprehend, but I was able to keep  
up with the majority of subjects. I would certainly like to attend next year.