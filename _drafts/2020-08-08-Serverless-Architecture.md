---
published: false
---
Today I want to start a small set of post, very "How To" oriented about what I have been working in the last months and really make me think that is the way to architect systems from now on: ***Serverless Architecture***. 

Serverless applications have been between us for a while already, but lately I have noted and big increase in the number of projects the people is working on, the number of posts you can find around, and in general I have the feeling that is getting a lot of traction.

But, what are Serverless Applications?

Apart from the obvious answer of *"An application that run without a server!"* that looks like the answer a colleague of your is going to give for trolling you a bit, the reality is that they run on a server, but, and here is the important thing, it is a server that you (almost) do not need to worry about, because your Cloud provider will be the responsible of provisioning, scaling and maintaining it, you just need to worry about the code.

[LEGENDARY PICTURE OF A CLOUD]

And why is this so important? Well, for me this is the big game changer in system architecture I have seen lately, because it helps with, or removes completely, one of the most difficult things you need to consider is to estimate the charge of your system, and then provision accordingly. Overprovisioning will cost you money, and doing a very tight provisioning will end up jeopardizing your system.
And thre is something even more interesting to note, if you have services in your system with a very small amount of charge, or even no charge for some periods of time, you are paying for them, no matter what.

Imagine for example you have some microservices dealing with different payment methods, some of them are local to some markets of yours, that are in use in certain time of the day, and then the rest of the day the usage is non existant. What do you do? 

***You go Serverless and when nobody is using them you do not pay.***

Because I think this is one of the most interesting points of Serverless, is that you pay when you compute, and when you do not compute you **do not pay**.

At the same time, if you have some unexpected peak, your system will be able to cope with it, no problem at all, because if you are using autoscaling you still need to spin your service, and you will have a time window where you won't be able to handle.

## Our Technical Task

So, more or less I tried to explain what is Serverless with some words, but I really prefer to explain the things with code and examples. And this is what is going to be this set of posts.

The technical stack we will use in the task will be the following:

* AWS: Cloudformation, Lambda, DynamoDB, EventBridge, API Gateway
* Kotlin: Amazon API V2
* Gradle with Kotlin DSL
* Serverless Framework, check this [post] for an informal presentation
* GithubActions for CI and CD

And the plan is to create a small application to show up how to wire everything up:

[PICTURE OF THE DIAGRAM]


As you can see we have 2 **Lambda Functions**, the first one, Lambda 1, will handle POST calls from **API Gateway** with the information of a customer, Name and Email, lets say, something basic.

Lambda 1 will be responsible of creating a new entry in **DynamoDB** if the customer is not registered already with an auto generated UUID and it will publish an event in **EventBridge** informing about the Registering of the new player. If the player was already registered, the lambda will return the existing UUID for the player, since we want our little system to be **Idempotent**. We will use **Transactions** for **DynamoDB** and we will make sure there is no double instertions.

### Serverless is not just *Lambda Functions*

Serverless is the concept of running systems without specific servers, so, DynamoDB, for example is a Serverless Database, you do not need to worry about provisitioning it (so you do not need to worry about OVER or UNDER provisioning) and the most important thing, you pay for what you use. The same applies to EventBridge, it is serverless, you do not need to provision anything.

We will go into more details, about these services in the next posts!

***Note***
Perhaps you are thinking, dude, what the hell? Why Kotlin? Why JVM? Everybody is using Node and Python!

I know is not the most common one, but it is the language by far I feel more comfortable working with, I also like Gradle and I write it using Kotlin DSL, so why not?

Cold start time, it is a b$tch




### AWS
[PICTURE OF AWS]
About AWS there is not too much to say, it is the most used Cloud provider at the moment, and they have a huge amount of services that you can use, in my case I use Lambda, DynamoDB, EventBridge along with CloudFormation, I am an advocate of Infrastructure as Code, as you can see in the other posts, and I really believe it is the way to go.

### Kotlin & Gradle.KTS
[PICTURE OF KOTLIN & Elephant]
Kotlin as a development language, I know is not the most common one, but it is the language by far I feel more confortable working with, I also like Scala a lot, and I'm always there between Kotlin and Scala, BUT I really prefer using Gradle instead of SBT, basically because I have been working with Gradle for the last 5 years and I can write it using Kotlin DSL, so I write it in my preferred language.

### Serverless Framework
[Picture of Serverless]
One of the biggest pain points when working with Serverless Architecture, actually I could even say, with the Cloud in general, is everything but coding, this is: Deployment, Testing and Logging, Management of the resources...

Serverless Framework takes care of a lot of things for you, and you will need to worry, almost, about coding, I will create a proper post about Serverless Framework because I think it really worth it. In this post I give a small introduction to it [post]

### GithubActions for CI and CD
[Picture of GithbActions]
I have my code in Github, and Github now offers GithubActions, free for Public repositories and a bunch of ours for Private ones.

And they are fantastic, the thing I like the most is that they are already in Github that is something I'm using already and that it is very easy to use, specially if fight for a bit with it and you understand how it works or you read a post like this one [post] where I explain how I set up and configured my project.












## Serverless Framework, a very informal presentation

We mentioned in the introductory post about Serverless Architecture that I use Serverless Framework, I would like to explain a bit why and the benefits it bring us.

>> Serverless Framework Open Source lets you develop and deploy serverless applications to AWS, Azure, GCP & more

So, going back to our introduction, I was saying that:

*One of the biggest pain points when working with Serverless, actually I could even say, with the Cloud in general, is everything but coding, this is: Deployment, Testing and Logging, Management of the resources...*

Apart from this, some things that are "basic" when you are coding a typical system, are painful as hell working with Serverless Architectures, at least in AWS, so for example, imagine you want to do something like:

* Create an Endpoint that expects a GET call and from a CustomerId parameter in the Path Params, answer with something, you also want typical logging capabilities.

So, this thing that it is a very common and simple task, that you code like in 5 or 10 lines requires:
 * The Lambda function: No surprise here
 * The API Gateway: You need to connect the HTTP GET with the param
 * Connect the API Gateway to the Lambda Funcion
 * A Cloud Watch Logging Group linked to the Lambda Function
 * The permissions in place to deploy and run all this stuff

If you do it *Cowboy Style*, in the console clicking here and there, then you are going to suffer, but if you want to do it in CloudFormation, you need to remember ALL the things you need to configure, do it right and also you will need to script how you are going to upload the code, this is a S3 bucket, so at the end, apart form the CloudFormation you will need to end up Scripting a CLI publisher of the code...

How, it sounds pretty harsh right? Because certanly it is, it is very hard, and very annoying, specially because they promised you this:

>> Serverless is the native architecture of the cloud that enables you to shift more of your operational responsibilities to AWS, increasing your agility and innovation. Serverless allows you to build and run applications and services without thinking about servers. It eliminates infrastructure management tasks such as server or cluster provisioning, patching, operating system maintenance, and capacity provisioning

It sounds soooo nice, but... what about the bloody DevOps???? You are saving in the infrastructure maintenance but the DevOps work simply went to the bloody moon! Tooling, Tooling and more Tooling!!! You cannot work *Cowboy Style* and use AWS Console! It is pointless! If you have 3 environments to manage: Prod, Test and Dev, for exmaple, what are you going to do? Go environment by environment clicking buttons, checking squares and filling text boxes? NO! You need to Automatise the stuff! And then, that beautiful quote I mentioned up there it is simply a ***LIE***, a huge one!

But then... the guys created Serverless Framework, where I'm a very small contributor but proud of it, and everything is peace:

`sls deploy`

This is what you need, and all you stuff goes straight to the Cloud (with capitals), your Lambdas are createad, your Cloud Watch loggin groups, your API Gateways, EVEN your DynamoDB tables, your indexes, ANYTHING you need, because, apart from the main part of our `serverless.yml` configuration file, that is the only file you need to touch apart of your typical code,  you can add pure CloudFormation stuff.





## Starting point, lets create our infrastructure layer
The idea of these posts will be to create a small test application, using my favourite stack, and it looks like the following:




Pre-requisites for our Adventure:
* AWS Account
* Serverless installed
* Github account where we will have our project (This will be its own post)




## Lets Write Some Kode
Kotlin && AWS in Serverless... We can all be amigos, right?

[Picture of Castro in the Simpsons]



## Continuous Integration and Deployment with Github Actions
[Small explanation of Continuous Integration and Continuous Deployment]

[Explain here HOW Github Actions works]

We have 2 files, the first one for Pull Requests and the second for Pushes to Master

```
name: CustomerAPI Pull Request

on:
  pull_request:
    branches: [ '*' ]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Grant execute permission for gradlew
      run: chmod +x gradlew
    - name: Build with Gradle
      run: ./gradlew clean build
```

We are going to explain all of these lines before going to the Push to Master, that is the big brother.

[Explanation of ALL the lines]


And now, the big brother
```
name: Deploy Master Branch

on:
  push:
    branches:
      - master

jobs:
  
  build:
    name: build
    runs-on: ubuntu-latest
    
    steps:

    - name: Allow repository check out with actions/checkout@v2
      uses: actions/checkout@v2

    - name: Installing Node 12  
      uses: actions/setup-node@v2-beta
      with:
        node-version: '12'    

    - name: Installing Serverless Framework 
      run: npm i -g serverless@1.77

    - name: Setting up Serverless Framework plugins (npm install)  
      run: npm install

    - name: Setting up Java 8  
      uses: actions/setup-java@v1
      with:
        java-version: 1.8

    - name: Making Gradle wrapper file executable (chmod +x gradlew)    
      run: chmod +x gradlew
      
    - name: Building project and deploying it using Gradle Wrapper & Serverless
      run: ./gradlew clean build deploy --stacktrace
      env:
        AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
        AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        SERVERLESS_ACCESS_KEY: ${{ secrets.SERVERLESS_ACCESS_KEY }}
```

[Explain ALL the lines]

[Explain the Environmnet]
