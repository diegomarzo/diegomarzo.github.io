---
published: false
---
In this post I would like to share my thoughts about what I think is the next step in system architecure. It is not really a future thing, it is something that is with us already, for some years, but what is true is most of the companies are still not working on it, or, lets say, they are sticking to the systems they have at the very moment, so change it is a bit difficult, imagine, a lot of companies are not even using the cloud, or ***The Cloud***, like that, in bold and italics, feels like deep stuff.

[LEGENDARY PICTURE OF A CLOUD]

This post won't be like super acurate or anything like that, is more of some kind of brain dump, and I think it is a good idea because I'm preparing a bunch of post about, 100% technical, with examples and with How To's about serverless, because it is precisely what I'm working on at the very moment.

For me, Serverless is the biggest game changer in System Architecture I have seen in a big while, but still I need to say it is not mature, I do not want to lie, sometimes I end up my day wanting to bang my head, but slowly you end up swiming through all the difficulties and finding your way and the bunch of technologies you really feel comfortable with. 

My tech stack at the moment it is a bit weird, but I'm feeling more and more confortable with it:

* AWS
* Kotlin
* Gradle
* Serverless Framework
* GithubActions for CI and CD

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

The idea of this set of Post will be to create a small test application, using my favourite stack, and it looks like the following:

[PICTURE OF THE DIAGRAM]











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

>> With Amazon Lambda and Serverless approach you will save a lot of time because you only need to worry to code your functionality and you won't need to worry about provisioning servers and taking care of infrastructure because it auto scale!!!

It sounds soooo nice, but... what about the bloody DevOps???? You are saving in the infrastructure maintenance but the DevOps work simply went to the bloody moon! Tooling, Tooling and more Tooling!!! You cannot work *Cowboy Style* and use AWS Console! It is pointless! If you have 3 environments to manage: Prod, Test and Dev, for exmaple, what are you going to do? Go environment by environment clicking buttons, checking squares and filling text boxes? NO! You need to Automatise the stuff! And then, that beautiful quote I mentioned up there it is simply a ***LIE***, a huge one!

But then... the guys created Serverless Framework, where I'm a very small contributor but proud of it, and everything is peace:

`sls deploy`

This is what you need, and all you stuff goes straight to the Cloud (with capitals), your Lambdas are createad, your Cloud Watch loggin groups, your API Gateways, EVEN your DynamoDB tables, your indexes, ANYTHING you need, because, apart from the main part of our `serverless.yml` configuration file, that is the only file you need to touch apart of your typical code,  you can add pure CloudFormation stuff.





## Starting point, lets create our infrastructure layer
The idea of these posts will be to create a small test application, using my favourite stack, and it looks like the following:



We will have 2 Lambda Functions, the first one, Lambda 1, will handle POST calls from API Gateway with the information of a customer, Name and Email, lets say, something basic.

The Email only can be used once, so, if the player is not already registered, we will generate an UUID and then we store it and then we will publish an event in EventBridge informing about the Registering of the new player. If the player was already registered, the lambda will return the existing UUID for the player.

Then we will have Lambda 2 what basically will listen to all the events and store them in a dynamo table, like in an event store.

Remember, this is an example that is why the architecture used is in reality too complex, but we want to show API Gateway, Lambda, DynamoDB and EventBridge, all of them working together!

And, all of this will be in one repository, since ALL the components are Serverless we do not need to worry about Scaling or provisioning, AWS will do for us!

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

