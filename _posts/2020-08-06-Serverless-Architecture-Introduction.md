---
published: true
title: 'An introduction to Serverless Architecture, How-to''s'
---
Today I want to start a small set of posts, very "How To" oriented about what I have been working in the last months and really make me think that is the way to architect systems from now on: ***Serverless Architecture***. 

Serverless applications have been between us for a while already, but lately, I have noted and a big increase in the number of projects the people is working on, the number of posts you can find around, and in general, I have the feeling that is getting a lot of traction.

But, what are Serverless Applications?

Apart from the obvious answer of *"An application that runs without a server!"* that looks like the answer a colleague of your is going to give for trolling you a bit, the reality is that they run on a server, but, and here is the important thing, it is a server that you (almost) do not need to worry about, because your Cloud provider will hold the responsibility of provisioning, scaling and maintaining it, you just need to worry about the code.

![legendary-cloud](/images/serverless/cloud.png)  
*Legendary picture of a cloud*

And why is this so important? Well, for me this is the big game-changer in system architecture I have seen lately, because it helps with, or removes completely, one of the most difficult things you need to consider is to estimate the charge of your system and then provision accordingly. Overprovisioning will cost you money, and doing very tight provisioning will end up jeopardizing your system.
And there is something even more interesting to note if you have services in your system with a very small amount of charge or even no charge for some periods of time, you are paying for them, no matter what.

Imagine for example you have some microservices dealing with different payment methods, some of them are local to some markets of yours, that is in use in certain time of the day, and then the rest of the day the usage is non-existent. What do you do? 

***You go Serverless and when nobody is using them you do not pay.***

Because I think this is one of the most interesting points of Serverless, is that you pay when you compute, and when you do not compute you **do not pay**.

At the same time, if you have some unexpected peak, your system will be able to cope with it, no problem at all, because if you are using autoscaling you still need to spin your service, and you will have a time window where you won't be able to handle.

## Our Technical Task

So, more or less I tried to explain what is Serverless with some words, but I really prefer to explain the things with code and examples. And this is what is going to be this set of posts.

The technical stack we will use in the task will be the following:

* AWS: Cloudformation, Lambda, DynamoDB, EventBridge, API Gateway
* Kotlin and Gradle: We are going to use the Amazon API V2 for Java
* Serverless Framework, check this [post] for an informal presentation
* GithubActions for CI and CD. We will dedicate a full post about how to make it work with all our tech stack, I suffered a lot, but I achieved excellence :D :D :D :D

And the plan is to create a small application to show up how to wire everything up:

![diagram](/images/serverless/diagram.png)  
*Diagram of the system we are going to implement*


As you can see we have 2 **Lambda Functions**, the first one, Lambda 1, will handle POST calls from **API Gateway** with the information of a customer, Name and Email, let's say, something basic.

Lambda 1 will be responsible for creating a new entry in **DynamoDB**. If the customer is not registered already with an auto-generated UUID and it will publish an event in **EventBridge** informing about the Registering of the new player. If the player was already registered, the lambda will return the existing UUID for the player, since we want our little system to be **Idempotent**. We will use **Transactions** for **DynamoDB** and we will make sure there are no double insertions.

### Serverless is not just *Lambda Functions*

Serverless is the concept of running systems without specific servers, so, DynamoDB, for example, is a Serverless Database, you do not need to worry about provisioning it (so you do not need to worry about OVER or UNDER provisioning) and the most important thing, you pay for what you use. The same applies to EventBridge, it is serverless, you do not need to handle provision for anything.

We will go into more details, about these services in the next posts!
