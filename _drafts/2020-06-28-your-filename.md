---
published: false
---
## Networks and CIRD notation

One of the things, as a developer, that I found when working with AWS and CloudFormation, was that a lot of the knoeldge I used to have about a lot of things related with Networking was really really rusty, I need to say that even I feel embarrased to ask my friend, top notch network engineer, even considering is my friend because they are very basic topics.

What is a **CIDR**?
>Classless inter-domain routing (CIDR) is a set of Internet protocol (IP) standards that are used to create unique identifiers for networks and individual devices.

It is a notation that allows network engineers to specify set of IP's, and it is formed by 2 blocks of numbers:
`A.B.C.D/E`
Where A.B.C.D represents an IP4 address and E represents the mask for it, the easiest way to understand it is with an example, and we have 3!

Our VPC Ip CIRD representation is 10.0.0.0/16

So the IP is 10.0.0.0 and the CIRD is 16, this means that the first 16 bits of the IP (remember every number in the IP4 is the representation of a Byte, this is 8 Bits) are going to be fixed for all our group of IPs, this is, all of them will start with **10.0.**, so for example 10.0.34.173 it is a valid IP that is part ouf our CIRD defined group, and for example 10.4.0.2 or 182.0.0.0 are not part of our group 

The same way, our Subnets are 10.0.0.0/24 and 10.0.1.0/24, these are 2 subnets, where the first 3 numbers define the group 10.0.0._ and 10.0.1_ 
Note that 24 is 3*8, 3 blocks of 8 bits = 24 bits)
