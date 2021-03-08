---
title: 'Setting up a basic Stripes framework'
date: '2009-11-08T20:48:00+10:00'
permalink: /setting-up-a-basic-stripes-framework
author: 'James Elsey'
category:
    - Java
tag: 
    - stripes
---
After being introduced to [Struts1](http://struts.apache.org/1.x/), [Struts2](http://struts.apache.org/2.x/) and [SpringMVC](http://en.wikipedia.org/wiki/Spring_Framework) at a very early stage in my development career, I was very happy to work on a project with [Stripes](http://www.stripesframework.org/display/stripes/Home). Stripes can do nearly everything I was doing with other action based frameworks, but is much simpler to use but is also a very versatile framework.

I’ve decided to put together this tutorial on how to setup a basic Stripes project with one page and one action, this will give you an idea of how the framework fits together, you can then expand on it as you wish.

Stripes is a very easy to use J2EE framework, similar to Struts but without all the heavy configuration XML files. Lets have a look at how easy it is to setup

Firstly, grab hold of an IDE such as [Netbeans](http://netbeans.org/). Install the standalone [Maven](http://maven.apache.org/), or use the embedded version that comes with Netbeans (or just grab the plugin). I won’t go into how to set this up right now, so you’ll have to figure that out yourself.

Once that’s setup, in Netbeans go File > New Project, choose a Maven Web project. This should give you a basic skeleton of a project.

So far, your new application has no idea about stripes, so we can go ahead and fix that, this is where we benefit from using Maven.

Open up the pom.xml that is located under Project Files, and add the following into the dependencies section. What this does is to imply that our project is going to make use of the stripes, jstl and taglibs libraries. Maven will detect this and upon saving will add those libraries to our project.

```xml
<dependency>
  <groupid>net.sourceforge.stripes</groupid>
  <artifactid>stripes</artifactid>
  <version>1.5.1</version>
</dependency>
<dependency>
  <groupid>jstl</groupid>
  <artifactid>jstl</artifactid>
  <version>1.1.2</version>
</dependency>
<dependency>
  <groupid>taglibs</groupid>
  <artifactid>standard</artifactid>
  <version>1.1.2</version>
</dependency>

```

The only XML configuration you’ll need to worry about (apart from pom.xml, but that doesn’t count) is the standard web.xml file that’ll be present in any Java web application, go ahead and add the following into your web.xml file, it’ll be located under WEB-INF

```xml
<?xml version="1.0" encoding="UTF-8"?>
  <web-app version="2.5" xmlns="http://java.sun.com/xml/ns/javaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">
    <display-name>MyProject</display-name>
    <filter>
      <filter-name>StripesFilter</filter-name>
      <filter-class>net.sourceforge.stripes.controller.StripesFilter</filter-class>
      <init-param>
        <param-name>ActionResolver.Packages</param-name>
        <param-value>com.jameselsey.myproject.action</param-value>
      </init-param>
    </filter>
    <servlet>
      <servlet-name>DispatcherServlet</servlet-name>
      <servlet-class>net.sourceforge.stripes.controller.DispatcherServlet</servlet-class>
      <load-on-startup>1</load-on-startup>
    </servlet>
    <filter-mapping>
      <filter-name>StripesFilter</filter-name>
      <servlet-name>DispatcherServlet</servlet-name>
      <dispatcher>REQUEST</dispatcher>
      <dispatcher>FORWARD</dispatcher>
    </filter-mapping>
    <servlet-mapping>
      <servlet-name>DispatcherServlet</servlet-name>
      <url-pattern>*.action</url-pattern>
    </servlet-mapping>
    <welcome-file-list>
      <welcome-file>/WEB-INF/jsp/hello.jsp</welcome-file>
    </welcome-file-list>
  </web-app>

```

Firstly, we declare a StripesFilter and specify which class it is from, this is part of the standard stripes setup so we can ignore that, however pay special attention to the ActionResolver parameter, that needs to match the “root” of our actions package, but we’ll come into that later.

Next we’ll need a DispatcherServlet declaration, and then we need to map stripes to intercept all requests via the DispatcherServlet. We also need to inform the servlet to filter all requests for .action. Lastly, we can specify a default page to be loaded when the application starts.

So now we have the stripes library available to us, so lets create an action and jump right in.

Create a new java class, such as com.jameselsey.action.MyHelloAction. Notice how I’ve put the action under the “action” package, the reason for this is so I can specify that package as the “root” action package in the web.xml file (check the web.xml code I posted above, the MyHelloAction will be available because it is within the package set in the ActionResolver)

Add the following into your action, you can rename variables if you like

```java
import java.util.Date;
import java.util.Random;
import net.sourceforge.stripes.action.ActionBean;
import net.sourceforge.stripes.action.ActionBeanContext;
import net.sourceforge.stripes.action.DefaultHandler;
import net.sourceforge.stripes.action.ForwardResolution;
import net.sourceforge.stripes.action.Resolution;

public class MyHelloAction implements ActionBean 
{

	private ActionBeanContext ctx;

    	public ActionBeanContext getContext() 
	{        
		return ctx;    
	}

    	public void setContext(ActionBeanContext ctx) 
	{        
		this.ctx = ctx;    
	}    

	private Date date;

	public Date getDate() 
	{        
		return date;    
	}

    	@DefaultHandler    
	public Resolution currentDate() 
	{        
		date = new Date();        
		return new ForwardResolution(VIEW);    
	}

    	public Resolution randomDate() 
	{        
		long max = System.currentTimeMillis();        
		long random = new Random().nextLong() % max;        
		date = new Date(random);        
		return new ForwardResolution(VIEW);    
	}    
	
	private static final String VIEW = "/WEB-INF/jsp/hello.jsp";

}

```

This action will generate a date, and a random date. Notice that the currentDate method is the default handler, that means that if we come into this action and don’t specify what event we want, the currentDate will be called. This would generate a date, and then return a forward resolution. You can see at the bottom of the action we have setup a String which contains the resolution to return, in this case a JSP

Right, so now we have our action which has generated some data for us, now lets setup a JSP to display this to a user. Create a jsp called hello.jsp</span> under WEB-INF/jsp, add the following

```xml
<%@page contentType="text/html;charset=ISO-8859-1" language="java" %>
<%@taglib prefix="s" uri="http://stripes.sourceforge.net/stripes.tld" %>
<%@taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>
      
        <html>
          <head>
            <title>Hello!</title>
          </head>
          <body>
            <h3>Hello James Elseys' Stripes Tutorial!</h3>
            <p>
              <b>
                <fmt:formatDate type="both" dateStyle="full"                                value="${actionBean.date}" />
              </b>
            </p>
            <p>
              <s:link beanclass="com.jameselsey.myproject.action.MyHelloAction"                    event="currentDate" >Show the current date and time</s:link>
              <s:link beanclass="com.jameselsey.myproject.action.MyHelloAction"                    event="randomDate" >Show a random date and time</s:link>
            </p>
          </body>
        </html>

```

Firstly, our JSP displays a date. As we have set a @DefaultHandler on our action, the currentDate gets served up in the JSP.

Underneath we have 2 links that go to our action class, these aren’t regular anchor links, we are making use of the stripes tags here. This allows us to bind a link to an action class, and to also specify an event. The event should match up to a method in the action class.


Once your all set, try and run the application! You should get something as in my following screenshot. Its now up to you to add more actions, pages, and other features such as Data Access Objects


Thanks for reading this post, apoligies for keeping it brief but I just wanted to note down how to setup the framework while its still fresh on my mind! I’ll try to keep more posted in the future as I’m starting my own [google-code](http://code.google.com/p/sales-tracker/) hosted project using stripes. If you’ve got anything to add, or need some help, please feel free to let me know!

