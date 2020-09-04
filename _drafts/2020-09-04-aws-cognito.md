---
published: false
---
AWS Cognito provide us with main components that are always super-tedious to implement in our applications but we need to have in place, these are:

- User creation, retreival and management
- Authentication of users: Login and Logout
- An UI to manage all of this without the need of implement it by ourselves
- Tools to control when an user is logged in or not in our system and this way provide functionality to it.

We are going to create a series of posts, because I think it is very interesting to have everything in place and because most of the resources I have found where really really light and thin and they do not explain the things in detail.

Lets see the different things we are going to create:

1) Cognito User Pool: Where our users are going to be stored and managed
2) Configure an application client in AWS Cognito User Pool side
3) Set up the Cognito UI
4) Put in place Authorizers to allow/disallow the access of the users depending on if they are logged in or not to the application.
5) Write down a Frontend in Vanilla HTML5/CSS3/Javascript to handle all and will be hosted in S3
6) Add SSL Certificates to our application and put in behind Cloudfront
7) Write down a Secured Backend application.



