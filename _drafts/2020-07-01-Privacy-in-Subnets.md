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
My elephant, always there to listen carefully

> Do you REALLY need to have Internet access in your Subnet?

I'm taking this break and telling you to ask yourselve simply because I was setting up in on of my systems a NATGateway just in "Cruise Control Cloud Formation Coding Style" and I realise, WTF, I don't need to have all of this, my Subnet is properly properly Private. Or perhaps it is a good moment to recheck your application architecture and see if you should create a new Private Subnet that will have internet access and leave al the modules you can in a Pure Private subnet 

So if the Answer is YES, I need to have access, we go ahead :)

The AWS "definition" of it:

> You can use a network address translation (NAT) gateway to enable instances in a private subnet to connect to the internet or other AWS services, but prevent the internet from initiating a connection with those instances. 

The first thing to have in consideration, the NAT Gateway needs to live in a Public Subnet, because it needs to have access to the Internet (what it is pretty obvious, since it is what we are going to use it for).

Another consideration is that, since we are going to access to it from another Subnet, we need to somehow provide it with an IP address, so it is accessible.

This IP requires to be fixed, so we can refer to it, this is achieved by using an ElasticIPAddress.

**Note** NAT Gateways are not for free, and Elastic IP Address are not free either, except when they are linked to a NAT Gateway, if you remove the NAT and you do not remove the Elastic IP you will be charged, and this is the beauty of Stacks and Cloud Formation, you create them together and you kill them together.

So lets go to CloudFormation!

First of all, lets create the ElasticIP address that we need to use:
```
ElasticIPAddress:
  Type: AWS::EC2::ElasticIPAddress
  Properties:
    Domain: VPC
```

Now we create the NAT Gateway
```
NATGateway:
  Type: AWS::EC2::NATGateway
  Properties:
    AllocationId: !GetAtt ElasticIPAddress.AllocationId
    SubnetId: !Ref PublicSubnet
    Tags:
      - Key: Name
      Value: !Sub NAT-${AWS::StackName}
```

Things to consider in the above script, is that we need to indicate the Subnet where we are going to put place the Gateway and also the AllocationId property. Since we do not know the IP itself that will be created by the ElasticIPAddress we need to reference it, but, in this case !Ref is not ok, becase we are not Referencing the component itself, like in the Subnet, we are referencing an attribute, for that task the intrinsic AWS function is `GetAtt`

Now that we have our NAT Gateway, we are going to create the Route table and the rule for it:
```
PrivateRouteTable:
  Type: AWS::EC2::RoutTable
  Properties:
    VpcId: !Ref VPC
    Tags:
      - Key: Name
        Value: Private
```

This Route Table is empty at the moment, we need to add rules to it:
```
PrivateRouteTableEntry1:
  Type: AWS::EC2::Route
  Properties:
    RouteTableId: !Ref PrivateRouteTable
    DestinationCidrBlock: 0.0.0.0/0
    NATGatewayId: !RefNATGateway
```

**Considerations**: If you have more than one private subnet you can still create only one private route table, and you can still have only 1 NATGateway, you do not really need more, unless you have a lot of outbound traffic and you want to split somehow the traffic between subnets or something very specific.


And thats all!!!

Our Diagram looks like:

The complete cloud formation template till now:

