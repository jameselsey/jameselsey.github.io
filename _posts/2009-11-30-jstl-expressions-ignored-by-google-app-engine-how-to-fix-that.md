---
title: 'JSTL Expressions ignored by Google App Engine, how to fix that..'
date: '2009-11-30T19:56:00+10:00'
permalink: /jstl-expressions-ignored-by-google-app-engine-how-to-fix-that
author: 'James Elsey'
category:
    - Java
tag:
    - jstl
---
So, you’ve written a nice J2EE application and have uploaded it onto the GAE only to be upset that your nice variables are displaying as ${contact.firstName} for example? No worries, its a simple issue, GAE seems to ignore expression language by default and all you need to do is to add the following line into your JSPs.

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" isELIgnored="false" %>
```

Commit that and you’re good to go ;)