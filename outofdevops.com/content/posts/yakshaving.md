---
title: "Yak Shaving and IaC"
date: 2022-01-05T10:48:38Z
description: "What is Yak Shaving? Is it waste of time or a need? What causes it and how to avoid it."
type: "post"
draft: false
image: "images/yak-shaving-and-iac/cover.jpg"
categories: 
  - "DevOps"
  - "Software Engineering"
tags:
  - "devops"
  - "softwareEngineering"
---


Are you familiar with the term **Yak Shaving**? It doesn't have anything to do with Yaks. 
**Yak Shaving** is a series of small tasks that you need to do before an activity can progress. 
If you are interested in its origin there is a short [blog post by Seth Godin](https://seths.blog/2005/03/dont_shave_that/) that explains it.

The term **Yak Shaving** is commonly used in software engineering in particular in the world of **DevOps**, 
it can negatively impact organisations, especially large ones. 

This blog post is a sort of transcript of my video:

{{< rawhtml >}}
<p style="text-align:center">
<iframe width="560" height="315" src="https://www.youtube.com/embed/bwrfp6Id9_4" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</p>
{{< /rawhtml >}}

Let me give you an example of Yak Shaving specific for **IaC**:

#### You want to create a load balancer.

This, apparently simple, 30 minutes task can take days, and it will come down to your organisation structure.
If you haven't experienced Yak Shaving, you may think that it's just impossible, isn't it?

Ok... Let's say we work in a medium-large organisation, with several departments each with their own responsibilities.
To create a Load Balancer we need the following resources:
- Load Balancer
- Forwarding Rules
- Certificates
- A bunch of Firewall rules for Health checks and backends
- Service Account with loadBalancerAdmin permissions to manage the LB

Now all these resource are managed by different teams and each of them has a "Protocol" to follow and rule to comply with.
You may have to coordinate with:
- Network team 
- Cyber Security team
- Network Security team
- Information Security team
- IAM team
- Regulatory compliance team

It's also very likely that you are not going to nail it at first attempt and you may have to change you code, and for each change 
you have this very slow feedback loop.

So now you should start to see how this can quickly take much longer than 30 minutes.

##### What causes it?
This is due to complexity of organisation and ways of working... essentially when teams/departments work in silos.
If each team follows their own protocols for FW rules, IAM, Security, etc..., this will happen sistematically.

##### How to avoid it?
First of all, with so many teams involved we should minimise what slows down the process. Top offenders here are meeting and 
people interactions.
Instead of just focusing on protocols to follow, that are specific for teams we should also focus on creating protocols for tasks.
For example we could create a protocol for Load Balancers.

##### How do you do this?
This can be achieved with automation and by following continuous delivery principles. 

Protocols defined as CD pipelines, can receive as input all the parameters for our LB.
In this way we can stadardise the process and all the teams involved have to take a much simpler decision 
(and in some cases they can even define policies as code in the form of steps of the CD pipeline).

So engineer yourself out of the way, comment like and subscribe and see you in the next video.

