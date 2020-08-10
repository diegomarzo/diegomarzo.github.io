---
published: false
title: 'Serverless Framework, a very informal presentation'
---
We mentioned in the introductory post about Serverless Architecture that I use Serverless Framework:

>> Serverless Framework lets you develop and deploy serverless applications to AWS, Azure, GCP & more

But, we cannot code without this framework? Without any framework? 

Well, one of the biggest pain points when working with serverless architectures, and I could even say, with the Cloud in general, is everything but coding, this is Deployment, Testing and Logging, Management of the resources... And this is where our framework helps us.

![serverless](/images/serverless/serverless.png)
*Serverless logo, it is pretty cool I need to say*

I would like to put you in the situation, and take in consideration what needs to be done when creating a Serverless system, and is that some things that are "basic" when you are coding a let's say, standard system, are painful as hell working with Serverless Architectures. At this point I would like to point something, I'm talking about "Cloud" and "Serverless systems" but I really mean AWS, that is the platform I know, that is what I really know, and I work with, so in other platforms, this could be different.

So for example, imagine you want to do something like:

* Create an Endpoint that expects a GET call and from a CustomerId parameter in the Path Params, answer with something, you also want typical logging capabilities.

So, this very common and simple task, that you can code in 5 or 10 lines requires:

 * The Lambda function: No surprise here
 * The API Gateway: You need to connect the HTTP GET with the param
 * Connect the API Gateway to the Lambda Function
 * A Cloud Watch Logging Group linked to the Lambda Function
 * The permissions in place to be able to deploy and run all this stuff

If you do it *Cowboy Style*, in the console clicking here and there, then you are going to suffer, but if you want to do it in CloudFormation, you need to remember ALL the things you need to configure, do it right and also you will need to script how you are going to upload the code, this is an S3 bucket, so at the end, apart from the CloudFormation you will need to end up Scripting a CLI publisher of the code...

How it sounds pretty harsh right? Because certainly, it is, it is difficult and annoying, especially because they promised you this:

>> Serverless is the native architecture of the cloud that enables you to shift more of your operational responsibilities to AWS, increasing your agility and innovation. Serverless allows you to build and run applications and services without thinking about servers. It eliminates infrastructure management tasks such as server or cluster provisioning, patching, operating system maintenance, and capacity provisioning

It sounds soooo nice, but... what about the bloody DevOps???? You are saving in the tasks of infrastructure and maintenance but the DevOps work simply went up to the bloody moon! 

You will need tooling, tooling and more tooling!!! You cannot work *Cowboy Style* and use AWS Console! It is pointless! If you have 3 environments to manage: Prod, Test and Dev, for example, what are you going to do? Go environment by environment clicking buttons, checking squares and filling text boxes? NO! You need to Automatise the stuff! And then, that beautiful quote I mentioned up there it is simply a bit pointless (and serverless).

But then... the guys created Serverless Framework, where I'm a small contributor, but proud of it, and everything is peace, you just to write:

`sls deploy`

This is all you need, and your stuff goes straight to the Cloud (with capitals), your Lambdas are created, your Cloud Watch log groups, your API Gateways, EVEN your DynamoDB tables, your indexes, ANYTHING you need, because, apart from the main part of our `serverless.yml` configuration file, that is the only file you need to touch apart of your typical code, you can add pure CloudFormation stuff.

![serverless-logo](/images/serverless/serverless-logo.png)
*https://www.serverless.com/*
