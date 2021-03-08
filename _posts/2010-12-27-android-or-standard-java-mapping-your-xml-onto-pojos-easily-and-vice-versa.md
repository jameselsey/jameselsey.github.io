---
title: 'Android or Standard Java; Mapping your XML onto POJOs easily, and vice versa'
date: '2010-12-27T23:02:26+10:00'
permalink: /android-or-standard-java-mapping-your-xml-onto-pojos-easily-and-vice-versa
author: 'James Elsey'
category:
    - Android
    - Java
tag:
    - dom
    - java
    - pojo
    - sax
    - xml
    - xstream
---
I’ve listed this post under Android, but to be honest this could apply to both J2SE and J2EE, its not really specific to Android but it was part of a requirement I was working with for a recent Android project.

The problem I had, was that I was getting an XML String as a response from a web service call. The String contained one long String representation of an XML object and I needed to get some data out of that. I could have manipulated the String manually and substring’d out what I required, but that would be incredibly poor and I would likely face verbal punishment from my peers.

Fortunately, [XStream ](http://xstream.codehaus.org/tutorial.html)comes to the rescue. Of course there are better methods such as [SAX ](http://en.wikipedia.org/wiki/Simple_API_for_XML)Parsing and [DOM](http://en.wikipedia.org/wiki/Document_Object_Model), but when you need a quick and dirty way of casting an XML String to an Object, and vice versa, this works nicely.

First off, you need to go to the XStream website and download the jar, then whack it on your class path so its available to you.

Then you need to do two things, it doesnt really matter which order, but you’ll need to :

1. Create the POJO that you wish to use. This will be a Java object representation of your data, your XML will get transformed to this
2. Create an XML Document that defines your data.

Lets have a look at the POJO first

```java
public class Person
{
  private String firstname;
  private String lastname;
  private Integer phone;
  private Integer fax;

  public String getFirstname()
  {
	  return firstname;
  }
  public void setFirstname(String firstname)
  {
	  this.firstname = firstname;
  }

  public String getLastname()
  {
	  return lastname;
  }
  public void setLastname(String lastname)
  {
	  this.lastname = lastname;
  }

  public Integer getPhone()
  {
	  return phone;
  }
  public void setPhone(Integer phone)
  {
	  this.phone = phone;
  }

  public Integer getFax()
  {
	  return fax;
  }
  public void setFax(Integer fax)
  {
	  this.fax = fax;
  }

}

```

And now look at the XML

```xml
<person>
  <firstname>Joe</firstname>
  <lastname>Walnes</lastname>
  <phone>778</phone>
  <fax>9000</fax>
</person>
```

Then, all you need to do is to convert between the two. I’ll show you a few examples in this little demo below, you should be able to just copy and paste it into an IDE such as Eclipse, IntelliJ or Netbeans.

```java
public class XStreamTest {

/**
* @param args
*/
public static void main(String[] args) {
//
XStream xstream = new XStream();
xstream.alias("person", Person.class);

Person joe = new Person();
joe.setFirstname("Joe");
joe.setLastname("Walnes");
joe.setPhone(1234-456);
joe.setFax(9999-999);

String xml = xstream.toXML(joe);
System.out.println(xml);

}

}
```

XStream is a great little utility class to let you get stuck in with XML and POJOs, but of course it does have its limitations. For a start you would need to include the XStream library in your Android application, increasing your APK file size, might not be an issue for small applications but could potentially cause problems.

If you need to quickly map a very simple POJO onto an xml then XStream may be your best choice, but if you want a more robust solution, its better to do it yourself and use DOM or SAX.