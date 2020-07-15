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

Or, we can put a bit of effort and set up the things properly, with a VPN, but not any VPN, here, in Lupita Corporation we are going to use **WireGuard**.

> Dude, WireGuard is like the Rolls Royce of VPNs

*My best-effort consultant being excited about VPNs and setting really high expectations*

## What is WireGuard and how I can set it up

![private_subnet](/images/isomorfism.png)
*Isomorphism between WireGuard and Rolls-Royce*

WireGuard, the Rolls-Royce of VPNs, it is not the typical VPN, was created by security expert Jason A. Donenfeld, and it is smaller and simpler than almost any VPN software. It implements something called IP Roaminng, this implicates that we do not need to set up the IP's of the clients in the server side, what is a huge and crazy advantage. 

The way it works, explaining in very simple terms, is by creating a Virtual Network Device, in the Server and in the Clients, this way, the traffic can be routed like we route normally the network traffic, and we do not need to do anything in the "VPN Server Software" appart of registering the Public Keys of the clients, because, all the traffic is encrypted.

Then in the client side, we just need to indicate the endpoint for the Server (this is PublicIP + Port) and what IP's are going to be redirected to the WireGuard Virtual Network Device.

We are going to set up our VPN now, in our laptop and our server, and the idea is that we will be able then, from our laptop to access all the elements of our VPC that we allow the access from our security groups, ip restriction policies... the basic and typical network administration, nothing special in the VPN Host.

So, lets explain a bit what we are going to do, and then we will do it all.

1) We are going to transform our Bastion into a VPN Host (you can also create, if you want another machine, but I will use the same one, just for simplicity).

2) We are going to configure the client connection to make sure it will be able to access the VPC elements (this is as simple as providing a private IP that is allowed to access the other servers)

3) We will install the client in our laptop.

4) Optional Step, we are going to set up the client in our Android Phone, simply because it is amazinly simple.

## Install WireGuard in our Bastion Server
So, first of all we are going to connecto to the bastion server (check previous post if you do not have it, but basically it is just create an instance in the VPC where we have access to the Internet Gateway and we are allowed to do SSH to)

```
$> ssh ubuntu@BASTION_PUBLIC_IP
```

Now, I'm going to do a small trick, instead of go manually step by step, I'm going to use a shell script that is based in Angry Stan (all the Kudos for him, my Script is basically a simplification removing the IP6 support because I do not have it in AWS and oriented to be deployed in a EC2 container, so the default values are a bit different)

```
$> curl -O https://raw.githubusercontent.com/diegomarzo/wireguard-install/master/wireguard-install.sh
$> chmod +x wireguard-install.sh
$> sudo ./wireguard-install.sh
```

Now, this will prompt stuff that you need to fill, and it is important to understand this, specially when you are inside a EC2 Instance and you want to provide VPN to the VPC

1) **Public external IPv4 address**: Not difficult, this is the PUBLIC IP of the Bastion, the same one you used to ssh into.

2) **Public interface, the WireGuard Virtual Network Device**: This will be created and all the traffic will go throught it, you could use the default value, something like **ens5**

3) **WireGuard interface**: This is the internal WireGuard interface, basically the name your server configuration will have, the default is fine, **wg0**

4) **Server's WireGuard IPv4**: You need to check that this is a valid value within your VPC, for example, my VPC is 10.0.0.0/16, so something like 10.0.100.1 (the default I pug) is inside my VPC.

5) **Server's WireGuard port**: By default I selected 60005, that is a private port, it is important to note that you will need to **OPEN** this port in your **SecurityGroup** for the Bastion (now promoted to VPN Host)

After this value the script will start to install all it needs and set up the computer. For example, there is something SUPER SUPER important, that when I tried to do by my own (without the script) I forgot, and I have check a TON of tutorials and nobody mentioned, and it is this thing:
```
net.ipv4.ip_forward = 1
```
The IP forwarding for IPv4, without it you won't be able to hoop into other servers, and that will make a bit pointless the stuff...

Now the script is back to you, to ask you about the Client Details, because what is going to do is Pregenerate all the Client Configuration, so the Client doesn't need to do ANYTHING at all.

6) **Client's WireGuard IPv4**: 10.0.100.2, This is the IP that WireGuard will give to the client that imports the file that it is generating, it is very important to realise that this IP is ALSO inside the VPC, and if we want to do anything about these IP's, like adding them to security groups and stuff like that, we can do it. Another SUPER important thing is to realise that the IP need to be one that is not already chosen by ANYBODY.

What I have done to make sure this IP's are not given to anybody is use a CIRD I'm not using for any Subnet. So, my Administrative Subnet is something like 10.0.10.0/24, and this VPN values I'm using are in 10.0.100.0/24, so there are no clashes (the other ones are 10.0.0.0/24, 10.0.1.0/24, ...) 

This part was one of the parts where I struggled a bit more I need to admit, untill I understood what's going on for real.

7) **VPC CIDR**: 10.0.0.0/16, that is my VPC CIDR, here the original script says something like Allow Ip's, and then everybody says 0.0.0.0/0 but this is not what we want and what we need for our task, we only want to redirect the traffic from the clients to the VPC when we ask for one of those IP's

BOOM!!!!!

All done now!!!!

Fantastic!

You will see in your screen now a QR Code, this QR code, and the link to a file, I'm going to just open the file and copy the stuff into a file in your local computer (you can remove it from the server now).

The file looks like:

```
[Interface]
PrivateKey = A PRIVATE KEY FOR THE CLIENT
Address = 10.0.100.2/32

[Peer]
PublicKey = THE SERVER PUBLIC KEY
PresharedKey = A PRESHARED KEY THAT WILL BE USED AS CLIENT PUBLIC KEY
Endpoint = PUBLIC_IP_OF_SERVER:60005
AllowedIPs = 10.0.0.0/16
```

So this file is for the Client, and it represents the **Client Interface** this is, the Virtual Network Card information and the information about the **Peer**, this is, the way out to the Server

## Client Work

First of all we will need the WireGuard client, to download it we go to https://www.wireguard.com/install/

Select our OS and proceed, once you have installed, we open it and we select "Import tunnel from file" and we load the file we created with the information we got from the server.

So now, if you activate your tunnel you will see that the Status is Active, and that you are sending data, BUT, you are not receiving any data at the moment, this is simply because our security group is blocking traffic from all ports except from ours, that is 60005, so we should open this port.

## Updating the VPN Host Security Group to allow traffic in port 60005

If you have selected other port, then you need to use it :)

Remember you can still block by IP here if you want, in the Security Group, that is the nice thin

This is how the Security Group ingress looks now:

```
  SecurityGroupIngress:
    - Description: "Access to SSH"
      IpProtocol: "tcp"
      FromPort: 22
      ToPort: 22
      CidrIp: !FindInMap ['IPsPerSecurityGroup', 'AdminDiego', 'allowedCidrIp']
    - Description: "Access to VPN"
      IpProtocol: "udp"
      FromPort: 60005
      ToPort: 60005
      CidrIp: !FindInMap ['IPsPerSecurityGroup', 'AdminDiego', 'allowedCidrIp']
```

So, if you deploy your stack, you will see straight forward, when it is done, if all went well that the traffic is going through.

To check that all is going well, you should be able to PING now the private IP of the Bastion:

```
$> ping 10.0.10.80
PING 10.0.10.80 (10.0.10.80): 56 data bytes
64 bytes from 10.0.10.80: icmp_seq=0 ttl=64 time=45.630 ms
64 bytes from 10.0.10.80: icmp_seq=1 ttl=64 time=47.281 ms
64 bytes from 10.0.10.80: icmp_seq=2 ttl=64 time=46.387 ms
64 bytes from 10.0.10.80: icmp_seq=3 ttl=64 time=52.606 ms
64 bytes from 10.0.10.80: icmp_seq=4 ttl=64 time=46.386 ms

```

And also ssh to it using the private IP:
```
$> ssh ubuntu@10.0.10.80
...
ubuntu@ip-10-0-10-80:~$
```

And the most important thing of all!!!!

You should be able to ping the private instance that is in the other subnet!

```
$> ping 10.0.3.209
PING 10.0.3.209 (10.0.3.209): 56 data bytes
64 bytes from 10.0.3.209: icmp_seq=0 ttl=63 time=53.569 ms
64 bytes from 10.0.3.209: icmp_seq=1 ttl=63 time=54.140 ms
64 bytes from 10.0.3.209: icmp_seq=2 ttl=63 time=74.778 ms
64 bytes from 10.0.3.209: icmp_seq=3 ttl=63 time=55.859 ms
64 bytes from 10.0.3.209: icmp_seq=4 ttl=63 time=84.657 ms
```

And also SSH into it:
```
$> ssh ubuntu@10.0.3.209
...
ubuntu@ip-10-0-3-209:~$
```

I need to say this, simply... **AMAZING**

*Note*: Another option is to use AWS LightSail and the cheapest instance (3.5$/Month), the bad thing is that it cannot be automatised with CloudFormation, so we lose the possibility to use IaC and if we need to connect it to the VPC you require a VPC Peering (There is no charge for setting up or running a VPC peering connection. Data transferred across peering connections is charged at $0.01/GB for send and receive, regardless of the Availability Zones involved.)

**References**:
- Angry Stan Wire Guard Script: https://github.com/angristan/wireguard-install
