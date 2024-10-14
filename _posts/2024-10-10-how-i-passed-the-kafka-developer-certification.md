---
title: "How I passed the Confluent Certified Developer for Apache Kafka"
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - kafka
---
After working with Kafka for about a year, I decided to take the Confluent Certified Developer for Apache Kafka exam as a way to help fill in any gaps in the skills I'd picked up. 

![](/assets/post_images/2024/ccdak.png)

I found out about the certification soon after I started working with kafka, and as a newcomer, I used the syllabus to guide my learning, I figured that if I studied the topics on the exam, I'd have at least a basic understanding of how it works.

I personally like to do the certifications for things that I'm working on, as it helps me to understand the technology better and to see if there are any areas that I need to improve on.

Here are my top resources

## Books
There's a lot of books out there, but these two are absolute must haves, the streams book was particularly good as it introduced concepts with working examples that you could relate to.

### [Kafka, the definitive guide](https://amzn.asia/d/6o9MbCz)
![](/assets/post_images/2024/kafkadefinitiveguide.jpg) 

I have seen this one referred to as "the bible for kafka", and they're not wrong. This book covers all of the basics that would be relevant to both the developer and administrator exams. I personally skipped over some of the administration sections as it wasn't so relevant for me, but the sections on topics, partitioning, connect, schema registry and security were all very useful. It's one of those books you'll read through quickly at first, but then keep referring back later on, mine is covered in sticky notes and highlights!

### [Kafka Streams in Action](https://amzn.asia/d/bZbwEBx)
![](/assets/post_images/2024/kafkastreamsbook.jpg) 

Whilst the definitive guide covers a lot, it doesn't go into enough detail on streams or ksqlDB, so this book is a must, consider them a pair. I really enjoyed the examples in this book, such as the tweet processor, I found it easy to follow. The second half of the book covers ksqlDB, which is invaluable for the exam.

## Courses
1. [Kafka 101 course](https://developer.confluent.io/courses/apache-kafka/events/)

   This is a free course from confluent, it's a great intro to kafka and only takes a few hours to get through, it'll cover all the topics at a high level, and even if you don't use the confluent platform, it's worth a watch. Confluent also have [plenty more videos](https://developer.confluent.io/courses/?course=for-developers#fundamentals) for specific areas  
2. [Cloud Guru deep dive](https://www.pluralsight.com/cloud-guru/courses/apache-kafka-deep-dive)
   
   At 10 hours this went into more detail than the kafka 101 course, it was useful, but not enough to pass the exam alone.
3. [Confluent Certified Developer for Apache Kafka](https://www.pluralsight.com/cloud-guru/courses/confluent-certified-developer-for-apache-kafka-ccdak)

   This course was the best one I found, at 22 hours it went into enough detail without overdoing it. It does have a good set of practice exams which I used half a dozen times before I started to unintentionally memorise the answers. The questions were well worded and en par with the real exam.

## Practice exams
1. [Udemy Practice exams](https://www.udemy.com/course/confluent-certified-apache-kafka-developer-practice-exams)

   I spent a lot of time here, there's 4 full practice tests. I found the questions to be about the same difficulty as the real exam, although there were a few poorly worded questions were it wasn't obvious what they were asking.

## Exam experience
I've done nearly a dozen exams at home now, all with Pearson Vue, but this was my first experience with Prometric. The checkin process was very in depth, the proctor wanted to know everything, I had to show underneath my desk and seat, I had to show behind my ears to prove there was no hidden ear piece and even had to empty all my pockets!

The exam itself was reasonably straightforward, I'd say that at least 50% of the questions were easy enough that when you've read the question you already know what the answer is before you've seen what the options are. There's probably around 10% really difficult questions and 40% you could just eliminate the obviously wrong answers to find the best guess.

I received an email with my results within about an hour, and the certificate by the next day.

## Closing thoughts
If your project is using kafka, then I'd recommend doing the certification for the same reasons as me, the sylabus will help you pickup on things you don't know. If you've been working with kafka for a while you could probably skip the courses and just grind out practice exams.