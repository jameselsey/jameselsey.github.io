---
title: 'Deploying the android libraries into your maven repository'
date: '2012-08-19T18:03:18+10:00'
permalink: /deploying-the-android-libraries-into-your-maven-repository
author: 'James Elsey'
category:
    - Android
    - 'Cloud'
tag:
    - android
    - cloudbees
    - continuous-integration
    - google
    - maven
---
Maven is a fantastic build tool, and a great addition to anyone developing on the android platform, however one of the first hurdles that people often stumble upon, is when their project involves one of the SDK libraries, such as Google Maps.

You’ll most likely see something like this when you attempt to first compile the project :

```
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 6.597s
[INFO] Finished at: Thu Jul 19 10:28:12 BST 2012
[INFO] Final Memory: 7M/81M
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal on project WifiSpotter: Could not resolve dependencies for project com.jameselsey.android.apps.wifispotter:WifiSpotter:apk:0.1-SNAPSHOT: Failed to collect dependencies for [com.google.android:android:jar:2.1.2 (provided), com.google.android:android-test:jar:2.2.1 (provided), com.pivotallabs:robolectric:jar:1.0 (test), junit:junit:jar:4.8.2 (test), com.google.android.maps:maps:jar:8_r1 (provided)]: Failed to read artifact descriptor for com.google.android.maps:maps:jar:8_r1: Could not transfer artifact com.google.android.maps:maps:pom:8_r1 from/to cloudbees-private-release-repository (https://repository-
[ERROR] jameselsey
[ERROR] .forge.cloudbees.com/release): IllegalArgumentException: Illegal character in authority at index 8: https://repository-
[ERROR] jameselsey
[ERROR] .forge.cloudbees.com/release/com/google/android/maps/maps/8_r1/maps-8_r1.pom
```

As we can see, maven is unable to find the maps artefact, and rightly so. This is because the Google jars are not available (at least not at the time of writing this) on the [maven central repositories](http://www.jarvana.com/jarvana/). Fortunately for us, its relatively easy to resolve this, we just need to obtain the artefacts from our android home area, and then install them to our maven repositories so our projects have access to them.

You could manually install all of them, but this would be quite a lengthy task, and relatively unnecessary with the help of the [maven-android-sdk-deploye](https://github.com/mosabua/maven-android-sdk-deployer/)r, this nifty little tool will extract the libraries from your local android SDK installation, and install them as maven dependencies, to your local repository. From there, you can just depend on them as any other maven artefact, such as :

```xml
<dependency>
  <groupId>android</groupId>
  <artifactId>android</artifactId>
  <version>4.1_r2</version>
  <scope>provided</scope>
</dependency>
```

There is no need for me to cover how to use this tool, since it is pretty well documented, however I would like to refer you to a [previous post of mine](/deploying-artefacts-into-your-cloudbees-maven-repositories) regarding deploying maven artefacts to a [CloudBees ](http://www.cloudbees.com/)repository, combined with this post, you can quite easily and reliably maven-ise the android dependencies, and deploy them into the cloud. Then you can build your android projects using maven from anywhere, and also take advantage of the free Jenkins service they provide.

Remember to drop in the repository into the pom.xml, and then you can simply run :

```
mvn clean install deploy:deploy
```

Hope this helps, if anything is unclear, please let me know!