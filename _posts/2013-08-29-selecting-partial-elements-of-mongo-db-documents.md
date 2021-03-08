---
title: 'Selecting partial elements of mongo db documents'
date: '2013-08-29T08:40:49+10:00'
permalink: /selecting-partial-elements-of-mongo-db-documents
author: 'James Elsey'
category:
    - 'Mongo DB'
tag:
    - document
    - json
    - mongo
    - mongodb
---
By default, when you find documents on mongo it returns the entire document, similar to what SELECT * FROM TABLE; would do in a relational world.

```
> db.user.find().pretty()
{
	"_id" : ObjectId("5217705cc2e6fc761c0b5371"),
	"userId" : "james",
	"age" : 27,
	"favouriteVideoGame" : "Skyrim"
}
```

You can retrieve partial document info by using something known as a projection ([see mongo docs here](http://docs.mongodb.org/manual/reference/method/db.collection.find/)). Use find() with an empty query, but specify the fields you want from the document like so:

```
> db.user.find({}, {userId:true})
{ "_id" : ObjectId("5217705cc2e6fc761c0b5371"), "userId" : "james" }

> db.user.find({}, {userId:true, age:true})
{ "_id" : ObjectId("5217705cc2e6fc761c0b5371"), "userId" : "james", "age" : 27 }
```

For the Java devs using Spring data, you can do a similar thing using the Query APIs, notably the fields().include() ([docs here](http://static.springsource.org/spring-data/mongodb/docs/current/api/)):

```java
Query q = Query.query(Criteria.where("userId").is(userId));
q.fields().include("age");

User score = mongoTemplate.findOne(q, User.class, "user");
```