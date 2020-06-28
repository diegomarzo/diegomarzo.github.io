---
published: false
---
## Defining a basic network design with AWS and CloudFormation

In this post we are going to see how to design a basic network for our company, we have several options to do it, one, for example, is to use the AWS Console and simply go and create our elements, and another option, that is what we think is the most recommended one is to use a CloudFormation template to script all the components that will be needed.

CloudFormation is the tool that allows us to model and the provision of our AWS systems. It is very interesting because it is based in the paradigm of "Infrastructure as Code" where we can define in a file, called Cloud Formation Template, all the elements that our system has, set up them and describe the relations between them. And when we want, we can deploy this **AWS Cloud Formation Template** (from now on *the template*) to create a **Stack**, that is the result of applying it.

And not only this! If we decide to update part of our stack, we just need to update the template, and since it is a file, we can use version control for it! Once the template has been updated, we just need to go to AWS CloudFormation and upload it again.

And if we want to delete all the elements, we can simply go to AWS  Cloudformation, select our stack, press "Delete" and... boom! It will delete all the elements defined in the stack!

Just before we see a bit of action I would like to hook up this post with the previous one where we talk about multiple environments to stress how important and positive is for us to have EVERYTHING we can in a CloudFormation template:
A CloudFormation template can be deployed in different environments, so, we do not need to be manually going through all our environments and creating with the console all the stuff we want, we just need to deploy our template! How cool is that!

*In a future post, we will explain how to specify different things for different environments, for example, the instance type of an **EC2** can differ between different environments.*

**So... enough talking and go to the action!**

## Cloud Formation Template creation

Lupita has very basic requirements, she simply wants to have 2 subnets in her system, one of them, public and one of them, private
This way her customers can access the elements placed on the public side and not on the private one.

*I recommend you to create a Repository where you are going to keep your AWS CloudFormation templates and only your templates. Because if you decide to automatise your template deployment you don't want to trigger stack creations just because you are changing an image, a document and, in general, anything that is related with your stacks*

You can use JSON format or YAML format, I will use YAML because it is less verbose than the JSON, at the same time you need to be much more careful with your spaces, the non use of tabs...

