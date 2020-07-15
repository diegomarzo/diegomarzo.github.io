---
published: false
---
So you already have a **VPC** that has been created by AWS for your, then you have created some **Subnets** there, and then you have an **Internet Gateway** in your VPC and you have connected some of your networks with your **route tables**.

We have created as well a **bastion machine** in previous port, and that way we can access the subnets via SSH. So, from an administration point of view we have a pretty solid setup.

But from the useage perspective, out set up has some strong limitations, for example something so simple as accessing a web application running in a subnet is not possible, we cannot even access  from our laptop to a simple HTTP server that is running in our VPC unless we expose it to the world, beause its IP is something like 10.0.3.12, so it is not accesible.

There are some options around, you can start creating some entry tables, security groups and hooking all up with the internet gateway, put some restrictions about the IPs and at the end of the day exposing all throught the internet.

But you will need to do this for ANY application or instance, and what about if you have someone new in the team? You will need to go and add this IP to everywhere, and then happens that... this IP is dynamic! So anytime he resets the router you will need to be updating the IP.
Or even worst! Lupita, our founder, wants to access the product demos from her mobile!!! 

We could, obviously, take the bad simple solution and say... f$$k it! Put all public behind an ugly url and do some praying to La Virgen de Guadalupe so nobody finds it...

Or, we can put a bit of effort and set up the things properly, with a VPN, but not any VPN, here, in Lupita Corporation we are going to use **WireGuard**

> Dude, WireGuard is like the Rolls Royce of VPNs

*My best-effort consultant being excited about VPNs and setting really high expectations*

## What is WireGuard and how I can set it up

WireGuard, the Rolls-Royce of VPNs it is not the typical VPN, it works a bit different, and I will try to explain in very short:

- WireGuard, Server and Client, creates a Virtual Network Device in every computer where it is present.
- We will define in the clients what data we want to send thought this Virtual Network Device and what will go throuht the typical Network Device.
- In the server we will assing IP's accordingly to every client within the VPN

So what we are going to do now is transform our Bastion Machine into a WireGuard Host Server

1) We are going to use here an Shell Script that is very useful, it is a fork from AngryStan, so a lot of Kudos to him (in the foot you have the original Script Link), where I have removed the IP6 support and also add some values that are more related with our task:





Every computer has their own Private Key and Public key that will be used to encrypt and decrypt the information that goes throught WireGuard.

One computer acts as a Host, and it is where we are going to install the WireGuard Server, this will be our EC2 Instance in the administrative network, and contains all the peer information.

The clients will have the public key of the server and will register their public keys into the server.


*Note*: Another option is to use AWS LightSail and the cheapest instance (3.5$/Month), the bad thing is that it cannot be automatised with CloudFormation, so we lose the possibility to use IaC and if we need to connect it to the VPC you require a VPC Peering (There is no charge for setting up or running a VPC peering connection. Data transferred across peering connections is charged at $0.01/GB for send and receive, regardless of the Availability Zones involved.)

**References**:
- Angry Stan Wire Guard Script: https://github.com/angristan/wireguard-install


