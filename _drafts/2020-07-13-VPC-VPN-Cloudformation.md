---
published: false
---
---
published: false
---
1) Reference elements of one stack from another one
 


2) Access to VPC using a VPN with EC2 & CloudFormation

There are several reason why you could want to access VPC private component, for example, you do not want anybody to access your DEV enviroment, or for example, here is a section of your system that you want to restrict access.

Pre-requisites:
We are going to use as a base our existing cloud formation template that can be find here:

*Note*: This cloud formation template has an output section we will use in this one with the VPC, the Route Table and the Internet Gateway.

A common solution for this is to configure a VPN, and then provide access to that VPN to certain users. And this that sounds pretty straight forward it is a bit painful in AWS. 

***Yes, there is a Client VPN, but it is not the easiest thing to set up, and it is not the cheapest way either, for example having a client endpoint active cost money (10 cents per hour) + you need to pay for connections (5 cents per hour), so just having a VPN endopoint for you, without using it is like 2.40$ every day. So not so cool.***

First lets create the Network infrastructure:



We are going to use OpenVPN to set up our VPN Host and we will see how to configure client access to it.



*Note*: Another option is to use AWS LightSail and the cheapest instance (3.5$/Month), the bad thing is that it cannot be automatised with CloudFormation, so we lose the possibility to use IaC and if we need to connect it to the VPC you require a VPC Peering (There is no charge for setting up or running a VPC peering connection. Data transferred across peering connections is charged at $0.01/GB for send and receive, regardless of the Availability Zones involved.)






3) VPN using OpenVPN and AWS LightSail, cheap machines for everyone!