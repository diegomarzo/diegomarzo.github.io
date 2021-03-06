---
layout: post
published: false
title: 'LightSail, cheap servers for everyone!'
image: /images/ls-logo.png
categories: [ aws, lightsail ]
tags: [ lightsail ]
---

What is AWS LightSail? It is Amazon's Simple Cloud Server. It is based mainly in the simplicity of usage, configure and cost-effective, aka Cheap as F$$k.

![image](/images/ls/ls-logo.png)

From the AWS page:

>Lightsail is best suited to projects that require a few virtual private servers and users who prefer a simple management interface. Common use cases for Lightsail include running websites, web applications, blogs, e-commerce sites, simple software, and more.

LightSail is not one of the services I see more around, and I need to say it has a bunch of things that obviously can push you back, first of all, is the interface... When you clicked on the LightSail icon, it opens a new tab/window and you can see how it looks... absolutely disconnected to the AWS common interface, feels like the idea of Amazon of "Simplicity of Usage" is to put a funky interface with a colourful robot.

![image](/images/ls/robot.png)
*The robot, we need to love him*

Said this, I like it, I like colours, and I like the robot, and the space rocket as well, but I don't like how they messed up with Roles and Account, it is really bad, first, you cannot change your role in LightSail, to do it, you need to close it, go to the main AWS Console, change it there and reopen the window from the standard AWS Console. Guys! Come on! Why I cannot switch the roles from there?, and by the way, if you change your role in your AWS Console, LightSail says, "Ey, I need to reconnect!" :(

I'm pestered about this because in Lupita Corporation we have our environments set up using sub-accounts within our main organisation, that is a common and recommended way to do it, and I have a "common" account where I have tooling.

Another thing is that you cannot use CloudFormation for your LightSail instances, what is not too nice, I like to have all my stacks controlled in CloudFormation so I can update them and delete them whenever I fancy, and I need to do an exception here.

I guess you will be thinking, Diego, and why the heck you are using this?

Simple, I love the Robot... Well, and because the cheapest server in the region I have my stuff, Frankfurt cost 3.5$ in a CPU that is equivalent to a t2.nano (what cost monthly 4.82$, to be fair you can get a bit better CPU with t3a.nano and it is cheaper, like 3.8$). 

![image](/images/ls/pricing.png)
*And this is what I really love about LightSail*

But the important thing is that includes:

* The SSD Storage, 20Gb, that is 2.40$ that you save in comparison with EC2 

* Transfer of 1Tb, depending on the nature of your application the price vary, but for example, if you transfer like 10 Gb to the Internet it is like 21$/Month, that you save as well!. All traffic extra cost like in EC2: 0.09$ Gb

So... 3.5$ instead of 3.8$ + 2.40$ + Transfer cost

(All calculations have been made using https://calculator.aws)

I think that makes a lot of sense to have LightSail instances on things you do not need to worry too much about them, like tools and services, let's say Jenkins, Nexus, WordPress or any other thing you want to put there and use as a service and that doesn't need 
to be in the VPC.

LightSail seems like a complete apart Cloud Provider, like I said at the beginning, I get this feeling of being in a Mini-Cloud, even the documentation is in GitHub Docs. 

It is much simpler, provides you with basic Network Functionality, Load Balance, Storage Disks and Event Data Bases for a very competitive price. And for this reason, no CloudFormation, no VPC, no heavy network handling... 

![image](/images/ls/other-options.png)
*Additional services offered*

If you need to connect to some of your resources, you need to create a VPC Peering, this peering is for free (and you do not pay to keep it) and you will only need to pay for the data transfer between your VPC and the LightSail system.

![image](/images/ls/happy-robot.png)
*Enjoy and have fun with the rocket and the robot!*
