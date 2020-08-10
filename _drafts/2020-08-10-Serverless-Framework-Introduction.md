---
published: false
---
## Serverless Framework, a very informal presentation

[SERVERLESS ICON HERE!]

We mentioned in the introductory post about Serverless Architecture that I use Serverless Framework, I would like to explain a bit why and the benefits it bring us.

>> Serverless Framework Open Source lets you develop and deploy serverless applications to AWS, Azure, GCP & more

One of the biggest pain points when working with Serverless, actually I could even say, with the Cloud in general, is everything but coding, this is: Deployment, Testing and Logging, Management of the resources...

Apart from this, some things that are "basic" when you are coding a typical system, are painful as hell working with Serverless Architectures, at least in AWS, that is what I really know, so for example, imagine you want to do something like:

* Create an Endpoint that expects a GET call and from a CustomerId parameter in the Path Params, answer with something, you also want typical logging capabilities.

So, this very common and simple task, that you code like in 5 or 10 lines requires:

 * The Lambda function: No surprise here
 * The API Gateway: You need to connect the HTTP GET with the param
 * Connect the API Gateway to the Lambda Funcion
 * A Cloud Watch Logging Group linked to the Lambda Function
 * The permissions in place to be able to deploy and run all this stuff

If you do it *Cowboy Style*, in the console clicking here and there, then you are going to suffer, but if you want to do it in CloudFormation, you need to remember ALL the things you need to configure, do it right and also you will need to script how you are going to upload the code, this is a S3 bucket, so at the end, apart form the CloudFormation you will need to end up Scripting a CLI publisher of the code...

How, it sounds pretty harsh right? Because certanly it is, it is difficult and annoying, specially because they promised you this:

>> Serverless is the native architecture of the cloud that enables you to shift more of your operational responsibilities to AWS, increasing your agility and innovation. Serverless allows you to build and run applications and services without thinking about servers. It eliminates infrastructure management tasks such as server or cluster provisioning, patching, operating system maintenance, and capacity provisioning

It sounds soooo nice, but... what about the bloody DevOps???? You are saving in the tasks of infrastructure and maintenance but the DevOps work simply went up to the bloody moon! 

You will need tooling, tooling and more tooling!!! You cannot work *Cowboy Style* and use AWS Console! It is pointless! If you have 3 environments to manage: Prod, Test and Dev, for exmaple, what are you going to do? Go environment by environment clicking buttons, checking squares and filling text boxes? NO! You need to Automatise the stuff! And then, that beautiful quote I mentioned up there it is simply a bit pointless (and serverless).

But then... the guys created Serverless Framework, where I'm a small contributor, but proud of it, and everything is peace, you just to write:

`sls deploy`

This is all you need, and your stuff goes straight to the Cloud (with capitals), your Lambdas are createad, your Cloud Watch log groups, your API Gateways, EVEN your DynamoDB tables, your indexes, ANYTHING you need, because, apart from the main part of our `serverless.yml` configuration file, that is the only file you need to touch apart of your typical code, you can add pure CloudFormation stuff.









## Starting point, lets create our infrastructure layer
The idea of these posts will be to create a small test application, using my favourite stack, and it looks like the following:




Pre-requisites for our Adventure:
* AWS Account
* Serverless installed
* Github account where we will have our project (This will be its own post)




## Lets Write Some Kode
Kotlin && AWS in Serverless... We can all be amigos, right?

[Picture of Castro in the Simpsons]






