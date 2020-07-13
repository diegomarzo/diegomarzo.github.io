---
published: false
---
---
published: false
---
1) Reference elements of one stack from another one
 
CloudFormation is great, and the whole idea of **Infrastructure as Code** as a developer, it is simply brilliant, you can play with all the stuff, and then, you can remove it if you want, or step ahead and update it, and AWS CloudFormation will take care you do not mess up (usually :)). But when you start doing bigger templates, you realise that you need to split them, for several reasons, that are very linked with why, as a developer, you want to extract functions and libraries:

- Shorter templates are easier to read
- Writing your infrastructure in several parts helps with iterations: you start creating your VPC and your subnets, then your routing tables, then your S3 buckets and policies...
- You can decouple elements of the configuration that are not coupled, for example, if you want to create a subnet for certain specifics (we will do a VPN Host server for example) it doesn't need to be along with the basic VPC configuration...

And here is when you realise something:

> Even when your templates are decoupled there are certain things that will require reference, for example your VPC Identifier will be needed any time you need to create a Subnet, or your main public route table will need to be updated to associate any new subnet that requires access to the internet and the same applies to any NAT Gateway.

And for this reason you have available the **Output section**.

The output section is the area of a CloudFormation template where you define and declare WHAT elements of your template need to be visible from the outside:

For example, in our main CloudFormation network template we will need to expose our VPC Identifier and the Route Table identifier, if we want to add, externally, another subnet.

And this is how it looks:

```
AWSTemplateFormatVersion: '2010-09-09'

####


The rest of our AWS Cloudformation template will be here


####

Outputs:
  VPCId:
    Description: VPC ID
    Value:
      Ref: VPC
    Export:
      Name:
        Fn::Sub: "${AWS::StackName}-VPCID"
  PublicRouteTableId:
    Description: Public Route Table ID
    Value:
      Ref: PublicRouteTable
    Export:
      Name:
        Fn::Sub: "${AWS::StackName}-PublicRouteTable"
```

It is pretty straight forward, at the end of the file (because it is easier to find and it is align with how usually you do in other development languajes like for example Javscript when creating modules, you create an **Output** section, and there you declare what you want to expose.

Since we want to expose the ID of the elements we use the entry **Ref:**, if for example we just want to expose a String from a mapping, we can do something like:

```
 Value: 
   FindInMap: ['IPsPerSecurityGroup', 'AdminDiego', 'allowedCidrIp']
```

**Note**: It is also acceptable to write the intrinsic fucntions straight forward, to generate a bit more compact code like this one:

```
Outputs:
  VPCId:
    Description: VPC ID
    Value: !Ref VPC
    Export:
      Name: !Sub "${AWS::StackName}-VPCID"
  PublicRouteTableId:
    Description: Public Route Table ID
    Value: !Ref PublicRouteTable
    Export:
      Name: !Sub "${AWS::StackName}-PublicRouteTable"
```

With those entries we have now available the elements as output. The next step will be simply to import them, to do this, we need to declare the Stack from where we are loading the data and then make use of a intrinsic function to read the outputs:

```
Parameters:
  NetworkStackName:
    Description: Main network stack including VPC and Route Table
    Type: String
    Default: main-network
```

It is important to note that we need to specify the main stack name, a recommened thing could be add a Default name here so we can import it easily from the CLI or from the Console, if not, we will need to navigate across the console to Copy&Paste the stack name, since the Console is not the friendliest interface around.

To use one of the values, we just need tu use the Fn::ImportValue, so for example to read the VPC Id we declared before (!Sub "${AWS::StackName}-VPCID") we just need to write down the following, when creating our new Subnet:

```
  SubnetAdmin:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !ImportValue !Sub "${NetworkStackName}-VPCID"
      CidrBlock: !FindInMap ['SubnetConfig', !Ref 'AWS::AccountId', 'SubnetAdminCIDR']
      AvailabilityZone: !Select [ 0, !GetAZs ]
      Tags:
        - Key: Name
          Value: !Sub ${AWS::StackName}-SubnetAdmin
```

So our complete Cloud Formation Template, including the Subnet and the entry for the Route Table will look like:

```
AWSTemplateFormatVersion: '2010-09-09'
Description:  Administrative subnet + Route table entry

Parameters:
  NetworkStackName:
    Description: Main network stack including VPC and Route Table
    Type: String
    Default: main-network

Resources:
  SubnetAdmin:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !ImportValue !Sub "${NetworkStackName}-VPCID"
      CidrBlock: 10.0.10.0/24
      AvailabilityZone: !Select [ 0, !GetAZs ]
      Tags:
        - Key: Name
          Value: !Sub ${AWS::StackName}-SubnetAdmin

  #Subnet table associations
  SubnetAdminRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref SubnetAdmin
      RouteTableId: !ImportValue !Sub "${NetworkStackName}-PublicRouteTable"
```


2) Access to VPC using a VPN with EC2 & CloudFormation

There are several reason why you could want to access VPC private component, for example, you do not want anybody to access your DEV enviroment, or for example, here is a section of your system that you want to restrict access.

Pre-requisites:
We are going to use as a base our existing cloud formation template that can be find here: [link to post that has external elements in the stack]

*Note*: This cloud formation template has an output section we will use in this one with the VPC, the Route Table and the Internet Gateway.

A common solution for this is to configure a VPN, and then provide access to that VPN to certain users. And this that sounds pretty straight forward it is a bit painful in AWS. 

***Yes, there is a Client VPN, but it is not the easiest thing to set up, and it is not the cheapest way either, for example having a client endpoint active cost money (10 cents per hour) + you need to pay for connections (5 cents per hour), so just having a VPN endopoint for you, without using it is like 2.40$ every day. So not so cool.***

First lets create the Network infrastructure:



We are going to use OpenVPN to set up our VPN Host and we will see how to configure client access to it.



*Note*: Another option is to use AWS LightSail and the cheapest instance (3.5$/Month), the bad thing is that it cannot be automatised with CloudFormation, so we lose the possibility to use IaC and if we need to connect it to the VPC you require a VPC Peering (There is no charge for setting up or running a VPC peering connection. Data transferred across peering connections is charged at $0.01/GB for send and receive, regardless of the Availability Zones involved.)






3) VPN using OpenVPN and AWS LightSail, cheap machines for everyone!
