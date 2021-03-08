---
title: 'Deploying a grails application to Cloudbees'
date: '2013-01-22T21:10:06+10:00'
permalink: /deploying-a-grails-application-to-cloudbees
author: 'James Elsey'
category:
    - 'Cloud'
    - Grails
tag: []

---
I spent much of last weekend experimenting with the grails framework, so I wanted to deploy what I had in the cloud. Theres a big tutorial on the [IntelliJ IDEA documentation ](http://blogs.jetbrains.com/idea/2012/12/deploy-web-apps-to-cloudbees-from-intellij-idea-12/)(the screenshots don’t seem to match my installation of IDEA, even though its the same version number).

There is a much easier way, providing you have the Cloudbees SDK installed, you can just run this one-liner :

```
grails war; bees app:deploy target/MyWarFile.war -a mycloudbeesusername/applicationcontainernamehere
```

The above will package the application as a war file, and then deploy it to your Cloudbees instance.

Obviously the IDE will bring some additional features to the deployment, but if you don’t care about that and just want to upload, the one liner wins hands down.