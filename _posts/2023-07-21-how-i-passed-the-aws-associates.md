---
title: "How I passed the 3 AWS associate exams the first time (and the cloud practitioner!)"
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - aws
  - certification
---

I recently passed the SysOps associate exam so I've collected "the three" as it's sometimes know (well, 4 if you include the cloud practitioner). Here's a few thoughts on how I did it, and what I used to study.

## Solutions Architect

![SAA](/assets/post_images/2023/badge_saa.png)

Most of the trainers and folk you'll meet on [r/AWSCertifications](https://www.reddit.com/r/AWSCertifications/) will suggest you start here. It gives you a good base to work from as this exam is quite broad and covers a lot of topics.

There's a lot of services to understand, but these ones I found to be the most important, or at least had the most questions in the exam

* EC2
* S3
* VPC
* IAM
* Route53
* Elastic Load Balancing
* CloudWatch
* CloudTrail

In terms of study material, I jumped through a few options. I started with ACloudGuru with some of Ryan Kronenbergs older content, then I moved to LinuxAcademy where I discovered Adrian Cantril. I then finished up
doing some mock exams on [TutorialsDojo](https://portal.tutorialsdojo.com/courses/aws-certified-solutions-architect-associate-practice-exams/), I was averaging about 80% on each of the tests.

I would also recommend reading through the [AWS Well Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/the-pillars-of-the-framework.html), it's a little bit dry, but there's some very valuable information in there and will absolutely be of help.

When I was feeling about 1 month away from being ready, I booked in the [Cloud Practitioner](https://aws.amazon.com/certification/certified-cloud-practitioner/) exam. Some people say this one is not worth doing if you're a technical person, but the exam is only US$100 and passing gives you a 50%
voucher for your next test, but I was primarily looking to get a feel for the exam format and the process of taking tests at home with the PearsonVue proctor.

## Developer

![DVA](/assets/post_images/2023/badge_dva.png)

By far the easiest exam for me, but I'm using dynamo, SQS, lambdas and several other developer services on a regular basis so it was mostly a case of brushing up on some of the parts I hadn't used much.

For this one I used the [Stephane Maarek](https://www.udemy.com/course/aws-certified-developer-associate-dva-c01/) course on Udemy, and then the mock tests on [Tutorials Dojo](https://portal.tutorialsdojo.com/courses/aws-certified-developer-associate-practice-exams/) and was able to pass with 945/1000

## SysOps Admin

![SOA](/assets/post_images/2023/badge_soa.png)

This one was considerably more challenging than the others, I had quite a gap of time before I got to this one (we managed to squeeze in a few more children which kept me too busy!), so I had to re-visit a lot of material, and then through the lens of a sysops admin. Personally I felt that this was more challenging because know what service to pick in a scenario is not enough, you need to know those services in enough detail that you can troubleshoot issues. With this exam, there could very well be questions where more than 1 answer is perfectly viable, but the question will be asking you for the most cost effective, or with least change. Sometimes the question omits that so you can pick the ideal world option.
The exam also has a very broad scope of services, you may get questions about routing policies in Route 53, and then a question about VPN gateways, then cloudformation, then cost management. I had questions around auto scaling tape gateways which I was not expecting.

I studied for this one using [Stephane Maareks](https://www.udemy.com/course/ultimate-aws-certified-sysops-administrator-associate/) Udemy course, then the mock tests on [Tutorials Dojo](https://portal.tutorialsdojo.com/courses/aws-certified-sysops-administrator-associate-practice-exams/) and was able to pass first time.

## Closing thoughts

I'm in a love-hate relationships with the certifications. I love the challenge, and enjoy being forced to broaden my horizons and learn about new services, but at the same time I'm a little frustrated with questions that shouldn't need to be memorised, like types of EBS volume, or how many usable IP addresses in a CIDR range, there are calculators for these things and knowing that isn't really a test of your AWS competence. Hopefully the labs will return soon and there is more emphasis on those in the future.
My SAA and DVA are due to expire this year, so after a short deviation into the Azure world, I'll loop back and renew those. I'll likely try Adrian Cantrils' courses as they are reasonably priced and get the best reviews. Udemy felt like death by powerpoint.


