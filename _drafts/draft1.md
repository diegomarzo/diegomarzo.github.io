---
published: false
---

## Create environments for your company with AWS Organizations.

So, one of the things you would like to have for sure, when setting up your systems would be to separate your environments.

My preferred option to do this is to make use of the AWS Organisations.

It is not a difficult thing to do but it is important to understand what are we doing :) 

**Important consideration**
AWS will create accounts that are linked to your main account and you won't be able to delete them in an easy fashion. So if you don't like the name of the account, you cannot simply remove it and recreate, you will need to log in the account and change from there, so be careful when choosing the name you want for it to avoid extra work!!!

### What we will have achieved at the end of this tutorial

- Our master account
	- mycompany
- Three sub-accounts that will be ready to hold Dev, Test and Prod environments
	- dev.mycompany
    - test.mycompany
    - prod.mycompany
- A third account that will hold common stuff, for example, images and other statics
	- common.mycomponany
- Role switching functionality in place to move between accounts

