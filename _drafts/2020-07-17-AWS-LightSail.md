---
published: false
---
What is AWS LightSail? It is the Amazon's Simple Cloud Server. It is based mainly in the simplicity of usage, configure and really cost-effective, aka Cheap as F$$k.

Not a lot of people uses LightSail, and I need to say it has a bunch of things that obvioulsy can push you back, first of all is the interface... When you clicked on the LightSail icon, it opens a new tab/window and you can see how it looks... absolutely disconnected to the AWS common interface, feels like the idea of Amazon of "Simplicity of Usage" is to put a funky interface with a colorful robot.

Said this, I like it, I like colors, and I like the robot, and the space rocket as well, but I don't like how they messed up with Roles and Account, it is really bad, first, you cannot change your role in LightSail, to do it, you need to close it, go to the main AWS Console, change it there and reopen the window (or do all in the console and refresh). Guys! Come on! Why I cannot switch the roles from there? 

I'm pestered about this, because in Lupita Corporation we have our environments set up using sub-accounts within our main organisation, that is a common and recommended way to do it, and I have a "common" account where I have tooling.

Another thing is that you cannot use CloudFormation for your LightSail instances, what is not too nice, I like to have all my stacks controlled in CloudFormation so I can update them and delete them whenever I fancy, and I need to do an exception here.

I guess you will be thinking, Diego, and why the heck you are using this?

Simple, I love the Robot... Well, and because the cheapest server in the region I have my stuff, Frankfurt cost 3.5$ in a CPU that is equivalent to a t2.nano (what cost monthly 4.82$, to be fair you can get a bit better CPU with t3a.nano and it is cheaper, like 3.8$). 

But the important thing is it includes the SSD Storage, 20Gb, that is 2.40$ with EC2 and 1Tb of Transfer, that depending on what are you doing with your instance will vary, but for example, if you transfer like 10 Gb to the Internet it is like 21$

(All calculations have been made using https://calculator.aws)

I really think makes sense to have LightSail instances on things you do not need to worry too much about them, like tools and services, lets say Jenkins, Nexus...


