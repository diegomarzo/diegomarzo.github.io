---
published: false
title: 'Public and Private Subnets, introduction'
---
The way AWS distributes the Network is with VPCs and then, inside them, you can create different Subnets that contains different elements of your system.

If we go back to our previous post about CloudFormation we mentioned our will to have a Private Subnets and Public Subnets.Lets understand a bit better what that really means:

* We call Public Subnet to a Subnet that has available access to the Internet from a Network perspective, this means that the Subnet will be able to perform OUTBOUND call to the Internet and receive INBOUND calls.

We can do several things to limit, for example, the INBOUND calls, via IP Whitelisting for example.

[PICTURE Here of public Subnet]

* A Private Subnet, in the other hand, is a Subnet that has no access to the Internet, it is basically Decoupled. Other Subnets in the VPC can access to it, and for that reason they make a lot of sense, you can have for example your EC2 instances there, running without access from the Outerworld, you can have your Databases and even your Lambdas there.

[PICTURE Here of private Subnet]


In the next posts we will see how to set up Public and Private subnets, and also how to have some special Hybrid between them: A Private Subnet with Internet Outbound capabilities.


