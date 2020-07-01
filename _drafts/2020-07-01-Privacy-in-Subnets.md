---
published: false
title: 'Private subnets, do I need a NAT Gateway?'
---
If we go back to our previous post about CloudFormation we mentioned our will to have a subnet private and a subnet public.

Lets understand a bit better what that really means.

We call Public Subnet to a Subnet that has available access to the Internet from a Network perspective, this means that the Subnet will be able to perform OUTBOUND call to the Internet and receive INBOUND calls.

We can do several things to limit, for example, the INBOUND calls, via IP Whitelisting for example.

A Private Subnet, in the other hand, is a Subnet that has no access to the Internet, it is basically Decoupled. Other Subnets in the VPC can access to it, and for that reason they make a lot of sense, you can have for example your EC2 instances there, running without access from the Outerworld, you can have your Databases and even your Lambdas there.

And all of this was very good.

Some times you want part of the elements of your system to access the internet, for example, a Lambda requires to access a third party service across the Internet, or the software running in your EC2 instance requires to download something...

For this cases, where we need to have OUTBOUND traffic but not INBOUND traffic, we can set up a NAT Gateway.

Now, before continuing I would like you to ask yourselve the following question, and if you have a Rubber Duck (or like me, a Playmobil Elephant, I need to talk with it when Lupita is not interested), try to explain to it what do you need

![elephant](/images/elephant.jpeg)

> Do you REALLY need to have Internet access in your Subnet?

I'm taking this break and telling you to ask yourselve simply because I was setting up in on of my systems a NATGateway just in "Cruise Control Cloud Formation Coding Style" and I realise, WTF, I don't need to have all of this, my Subnet is properly properly Private. Or perhaps it is a good moment to recheck your application architecture and see if you should create a new Private Subnet that will have internet access and leave al the modules you can in a Pure Private subnet 

So if the Answer is YES, I need to have access, we go ahead :)




, but, what happens if you want to, from a Lambda, for example, perform a call to a third party service that it is in the Internet? Or from your application running in a EC2 instance?

Here is where the NAT Gateways come to our rescue.

But before going ahead you always need to make yourself the following question:
Do I REALLY need to access 
