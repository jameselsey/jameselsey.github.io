---
title: 'Tutorial; How to call Yahoo! REST web services the easy way!'
date: '2010-10-05T12:55:15+10:00'
permalink: /tutorial-how-to-call-yahoo-rest-web-services-the-easy-way
author: 'James Elsey'
category:
    - Java
tag:
    - api
    - REST
    - 'web service'
    - yahoo
---
I’ve got some requirements where I need to call a REST web service from an Android device, so I figured I’d get back to basics, and try to call a REST service through a simple Java application, just to see if I can get it working.

After trawling the web for what seemed like hours, I was finally able to come up with a simple yet effective solution. Consider the following

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;

/**
 * This simple class demonstrates how to call a 
 * REST web service provided by Yahoo! Developer Network APIs
 * @author james.elsey
 * @date 05/October/2010
 */
public class RestTest {

	public static void main(String[] args) 
	{
		
		String yahooAppId = "sam.A2DV34EOgopFWeVeqLVnaoTVmEIVZnVtd58o5IRCsZ4kcwBTSp4gVl9E7hnnbRxDgRo-";
		String URL = "http://where.yahooapis.com/geocode?location=701+First+Ave,+Sunnyvale,+CA&appid=";

		String URLtoCall = URL + yahooAppId;
		
		try 
		{
			String responseString = httpGet(URLtoCall);
			
			// Do something with the returned response, currently just printing to console
			System.out.println(responseString);
		} 
		catch (IOException e) 
		{
			// Handle exception here, currently just printing stack trace
			e.printStackTrace();
		}

	}
	
	/**
	 * Call URL and buffer response into a String
	 * @param urlString URL to be called
	 * @return String The response XML String
	 * @throws IOException
	 */
	public static String httpGet(String urlString) throws IOException 
	{
		  URL url = new URL(urlString);
		  HttpURLConnection conn =
		      (HttpURLConnection) url.openConnection();

		  // Check for successful response code or throw error
		  if (conn.getResponseCode() != 200) 
		  {
		    throw new IOException(conn.getResponseMessage());
		  }

		  // Buffer the result into a string
		  BufferedReader buffrd = new BufferedReader(
		      new InputStreamReader(conn.getInputStream()));
		  StringBuilder sb = new StringBuilder();
		  String line;
		  while ((line = buffrd.readLine()) != null) 
		  {
		    sb.append(line);
		  }

		  buffrd.close();
		  conn.disconnect();
		  return sb.toString();
	}
	
}

```

This simple piece of code will create a URL to be called, call it, and print out the result which in this case is an XML String. This is a particualry basic way of consuming a REST service, and using 3rd party libraries such as [Apache HTTP Commons](http://hc.apache.org/httpclient-3.x/) may provide a cleaner and more effective solution

This will give you a XML String response, its up to you how you deal with the response, you can parse it using [SAX ](http://en.wikipedia.org/wiki/Simple_API_for_XML)for example. In my next tutorial I’ll look at parsing, and how we can intergrate this all with an Android application

I was quite impressed with the documentation and support provided by the [Yahoo! Developer Network](http://developer.yahoo.com/), I will certainly be using this more frequently as their web services are easier to use than Googles, which is an odd occasion