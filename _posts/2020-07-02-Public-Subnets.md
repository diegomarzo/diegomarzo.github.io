---
layout: post
published: true
title: Public subnets and Internet Access
image: /images/public_subnet_icon.png
categories: [ aws, networks ]
tags: [ vpc, subnet ]
---

In this post, that is the second in the series of 3 about Subnets, we will cover **Public Subnets**. We call **Public Subnet** to a Subnet that has available access to the Internet from a Network perspective, this means that the Subnet will be able to perform OUTBOUND call to the Internet and receive INBOUND calls. By default, when a Subnet is created, it is created as a private one, it is not hook to the internet, and when we need access from the outside to it and we need to access the internet from it we need to explicitly create the required AWS resources.

![image](/images/public_subnet_icon.png)
*Public Subnet*

### Public route tables and Route entries

If we remember our first AWS [post](https://diegomarzo.github.io/Basic-network-cloudformation-templating/), where we created the VPC and the Subnets we also  added these two components:
```
  InternetGateway:
    Type: AWS::EC2::InternetGateway
    DependsOn: VPC
  AttachGateway:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref VPC
      InternetGatewayId: !Ref InternetGateway
```

This, provides the VPC the capability to connect with the Internet, in order to make our Subnet Public we need to hook it up with this Internet Gateway, and this is donde by creating a Route Table, we are going to call it PublicRouteTable, since we can reuse it in any Public Subnet we want to create.

```
  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref VPC
      Tags:
        - Key: Name
          Value: Public
```

The AWS:EC2::RouteTable creates an EMPTY route table, so now we need to create the entry that will allow to access the InternetGateway

```
  PublicRouteTableEntry1:
    Type: AWS::EC2::Route
    DependsOn: AttachGateway
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref InternetGateway
```

**Important** We need to add the `DependsOn` here because if we try to Add the Route to a component that doesn't exists it will fail, remember this doesn't happens with teh PublicRouteTable because we are using !Ref, and we know that using !Ref CloudFormation makes sure of the order creation.

### Subnet route table association

Finally we are going to connect our PublicSubnet to the RouteTable we have created:

```
  PublicSubnetRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref PublicSubnet
      RouteTableId: !Ref PublicRouteTable
```

The SubnetRouteTableAssociation allows us to associate a Subnet with a RouteTable

This is the representation of our system now in the CloudFormationTemplate:

![diagram](/images/diagram_with_public.png)
*AWS Cloud Formation diagram*


### Complete cloud formation template

```
AWSTemplateFormatVersion: '2010-09-09'
Description:  This is the stack for the network in Lupita Enterprises
# CIRD groups that will be used by the resources    
Mappings:
  SubnetConfig:
    VPC:
      CIDR: 10.0.0.0/16
    PublicSubnet:
      CIDR: 10.0.0.0/24 
    PrivateSubnet:
      CIDR: 10.0.1.0/24
Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: !FindInMap ['SubnetConfig', 'VPC', 'CIDR']
      EnableDnsSupport: true
      EnableDnsHostnames: true
      Tags:
      - Key: Name
        Value: !Sub ${AWS::StackName}-VPC
  #Subnets
  PublicSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      CidrBlock: !FindInMap ['SubnetConfig', 'PublicSubnet', 'CIDR']
      AvailabilityZone: !Select [ 0, !GetAZs ]
      Tags:
        - Key: Name
          Value: !Sub ${AWS::StackName}-PublicSubnet
  PrivateSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      CidrBlock: !FindInMap ['SubnetConfig', 'PrivateSubnet', 'CIDR']
      AvailabilityZone: !Select [ 1, !GetAZs ]
      Tags:
        - Key: Name
          Value: !Sub ${AWS::StackName}-PrivateSubnet
  InternetGateway:
    Type: AWS::EC2::InternetGateway
    DependsOn: VPC
  AttachGateway:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref VPC
      InternetGatewayId: !Ref InternetGateway
  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref VPC
      Tags:
        - Key: Name
          Value: Public
  PublicRouteTableEntry1:
    Type: AWS::EC2::Route
    DependsOn: AttachGateway
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref InternetGateway
  PublicSubnetRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref PublicSubnet
      RouteTableId: !Ref PublicRouteTable

```
