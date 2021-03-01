---
title: "Spring annotations not working? Common gotcha in Spring"
excerpt_separator: "<!--more-->"
categories:
  - Java
tags:
  - java
  - spring
---

There's a very common gotcha with Spring annotations, it's caught me out in the past, and is certainly one of the things that I check when reviewing pull requests. Have a look at this example.

```java
public class MySpringService {

  public void doSomething(){
    // do some stuff, then update the DB
    updateDb();
  }
  
  @Transactional
  public void updateDb(){
    // update the db with something
  }
  
}
```

We have 2 methods in the same class, one calls another. Whilst this looks fine to the untrained eye, the `@Transactional` will be ignored, since the method is called directly from the same class. By making a direct call, we bypass the Spring IoC, so it doesn't have a chance to intercept and run the annotations.

To solve this, we can move the `updateDb` out into it's own class, so the call would be managed by Spring and it can make use of the annotation, like so.

```java
@Component
public class MySpringService {

  @Autowired
  private MyDbService myDbService;
  
  public void doSomething(){
    // do some stuff, then update the DB
    myDbService.updateDb();
  }

}

@Component
public class MyDbService{

  @Transactional
  public void updateDb(){
    // update the db with something
  }
  
}
```

I've used the example of `@Transactional` as it's easy to understand, but the same would apply to other annotation types.
