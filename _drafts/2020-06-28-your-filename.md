---
published: false
---
## Networks and CIRD notation

One of the things, as a developer, that I found when working with AWS and CloudFormation, was that a lot of the knowledge I used to have about a lot of things related with Networking was really really rusty, also basic stuff. 

I need to say that even I feel embarrassed to ask my friend, top-notch network engineer because they were so basic!.

But then you are checking something with another developer, and one of these topics appear, and you look at each other like... man, do you know what the heck is this?

So I'm not ashamed to say that one of these things was the CIDR notation, so I think a pretty basic post can be handy.

### IP4 and masking

Ok, everybody has seen IP4 addresses, they look something like this: 216.58.200.1

There are 4 blocks of numbers, between 0 and 255, and this is because every block is the decimal representation of 8 bits = 1 Byte

Our address will be then:
11011000.00111010.11001000.00000001


So, if we have a set of IPS, for example, that go from 216.58.200.0 to 216.58.200.255, we need a way of representing this range, and perhaps you remember setting up some old network to play some LAN Quake you already identified that what I put is equivalent to:

IP4: 216.58.200.0
SubnetMask: 255.255.255.0

What **CIDR** means?
>Classless inter-domain routing (CIDR) is a set of Internet protocol (IP) standards that are used to create unique identifiers for networks and individual devices.

This is when the CIDR was defined, for example, that range can be expressed like:

216.58.200.0/24

And this is a much more compact way of expressing a group of IP's

What it means is, from the 32 bits that represent an IP4 address you are going to have a group where the first 24 are the same for all of them.


In our CIDR Mapping that can be found in the CloudFormation Post we have this table:

```
  SubnetConfig:
    # General VPC
    VPC:
      CIDR: 10.0.0.0/16
    # Operators subnet  
   SubnetAPublic:
      CIDR: 10.0.0.0/24 
   SubnetBPrivate:
      CIDR: 10.0.1.0/24 
```

The VPC will he a subnet of 2^16 addresses, all of them starting with "10.0", and having this subnet mask: 255.255.0.0
16 = 2 * 8 => The 2 first blocks are common

The SubnetAPublic will have 2^8 addresses, all of them starting with "10.0.0" and having this subnet mask: 255.255.255.0
24 = 3 * 8 => The 3 first blocks are common

The SubnetBPublic will have 2^8 addresses, all of them starting with "10.0.1" and having this subnet mask: 255.255.255.0
24 = 3 * 8 => The 3 first blocks are common


**Note:** It is normal to forget the stuff we do not use, do not be ashamed like me :). Perhaps your super brilliant network engineer friend doesn't know how to reverse an array in Python, and he should not be ashamed of that :).

