---
title: 'Modify your Java web application to run on Google App Engine'
date: '2009-11-21T16:58:00+10:00'
permalink: /modify-your-java-web-application-to-run-on-google-app-engine
author: 'James Elsey'
category:
    - Java
tag: 
    - Stripes
---
So, you have a Java web application that you would like to host somewhere for free, no worries, Google to the rescue. What is Google App Engine? Well to quote from their site…

> Google App Engine lets you run your web applications on Google’s infrastructure. App Engine applications are easy to build, easy to maintain, and easy to scale as your traffic and data storage needs grow. With App Engine, there are no servers to maintain: You just upload your application, and it’s ready to serve your users.
> 
> You can serve your app from your own domain name (such as http://www.example.com/) using Google Apps. Or, you can serve your app using a free name on the appspot.com domain. You can share your application with the world, or limit access to members of your organization.
> 
> Google App Engine supports apps written in several programming languages. With App Engine’s Java runtime environment, you can build your app using standard Java technologies, including the JVM, Java servlets, and the Java programming language—or any other language using a JVM-based interpreter or compiler, such as JavaScript or Ruby. App Engine also features a dedicated Python runtime environment, which includes a fast Python interpreter and the Python standard library. The Java and Python runtime environments are built to ensure that your application runs quickly, securely, and without interference from other apps on the system.

As an overview, you will need to do the following:

1. Read the GAE Documentation
2. Download the GAE SDK
3. Alter your application
4. Test this locally using the GAE development server (as included with GAE SDK)
5. If it all works, upload it using the AppCfg command line utility

The GAE SDK

Go ahead and download the GAE SDK, unzip this somewhere on your local hard drive, in my case this was /home/james/Development/utils/appengine-java-sdk-1.2.6

Altering your application

Next, you need to add the app engine libraries to your application, I’m using maven2 purely for ease of use, so I added this to my pom.xml file

```xml
        <dependency>
           <groupId>com.google.appengine</groupId>
           <artifactId>appengine-java-sdk</artifactId>
           <version>1.2.6</version>
       </dependency>

```

When you try to build the application, maven won’t be able to automatically download the jar file, so you’ll have to install that manually using the following command

```
mvn install:install-file -DgroupId=com.google.appengine -DartifactId=appengine-java-sdk -Dversion=1.2.6 -Dpackaging=jar -Dfile=lib/user/appengine-api-1.0-sdk-1.2.6.jar
```

You’ll notice that the actual jar file you need is under lib/user/ from the SDK you just downloaded.

You will need to create the appengine-web.xml file, this should reside in the same place as your standard web.xml file, under WEB-INF. The purpose of this file is to inform the GAE which application this belongs to, so you will need to add your GAE application ID into the example below, in my case it was sales-tracker

```xml
<appengine-web-app xmlns="http://appengine.google.com/ns/1.0">
   <application>sales-tracker</application>
   <version>1</version>
</appengine-web-app>
```

Provide an empty implementation of MultipartWrapper

The next thing that you need to do is to edit your web.xml to include the MultipartWrapperFactory, add the following into the init-param section

```xml
<init-param>
          <param-name>MultipartWrapperFactory.Class</param-name>
          <param-value>com.jameselsey.salestracker.util.EmptyMultipartWrapper</param-value>
      </init-param>

```

The next thing we need to do is to provide an implementation of the MultipartWrapper, it doesn’t matter where in your package structure you take care of this, providing it matches up to the declaration in your web.xml, as shown above

```java
package com.jameselsey.salestracker.util;

import java.io.IOException;

import javax.servlet.http.HttpServletRequest;

import net.sourceforge.stripes.config.Configuration;
import net.sourceforge.stripes.controller.FileUploadLimitExceededException;
import net.sourceforge.stripes.controller.multipart.MultipartWrapper;
import net.sourceforge.stripes.config.ConfigurableComponent;
import net.sourceforge.stripes.controller.multipart.MultipartWrapperFactory;

/**
* GAE has no support for uploading of files, so we use this to disable that part of Stripes
*
* @author james.elsey
*/
public class EmptyMultipartWrapper implements ConfigurableComponent, MultipartWrapperFactory {

  /**
   * @see net.sourceforge.stripes.config.ConfigurableComponent#init(net.sourceforge.stripes.config.Configuration)
   */
  public void init(Configuration conf) throws Exception {
  }

  /**
   * @see net.sourceforge.stripes.controller.multipart.MultipartWrapperFactory#wrap(javax.servlet.http.HttpServletRequest)
   */
  public MultipartWrapper wrap(HttpServletRequest request) throws IOException, FileUploadLimitExceededException {
      return null;
  }
}


```

Upload your project to GAE

Your application should be ready to go now, build your application using

```
mvn clean package
```

Then upload it to GAE using, you have to update the war folder not the actual war file

```
./AppCfg.sh update ~/Development/projectname/target/myprojectname
```

Thats pretty much it! Good luck!