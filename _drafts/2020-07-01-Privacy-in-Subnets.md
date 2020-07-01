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

So before continuing to explain our fantastic NAT Gateways I will ask yourself to do the following question:

Do you REALLY need to have Internet access in your Subnet?

Sometimes we end up falling into the Cargo Cult  AWS Cloudformation approach

, but, what happens if you want to, from a Lambda, for example, perform a call to a third party service that it is in the Internet? Or from your application running in a EC2 instance?

Here is where the NAT Gateways come to our rescue.

But before going ahead you always need to make yourself the following question:
Do I REALLY need to access 
