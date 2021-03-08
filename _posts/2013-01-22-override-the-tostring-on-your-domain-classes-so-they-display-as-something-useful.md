---
title: 'Override the toString on your domain classes so they display as something useful'
date: '2013-01-22T21:06:34+10:00'
permalink: /override-the-tostring-on-your-domain-classes-so-they-display-as-something-useful
author: 'James Elsey'
category:
    - Groovy
tag:
    - domain
    - grails
    - groovy
---
If you’re finding that your domain objects are not being displayed in a readable manner, chances are its because they haven’t been told to. This is often the case in drop down menus that the grails scaffolding creates. You can easily fix this by overriding the toString method on your domain class, such as the following.

```groovy
class Question {

    String text

    static hasMany = [responses: Response]

    static constraints = {
        text()
        responses()
    }

    String toString(){
        return text
    }
}
```