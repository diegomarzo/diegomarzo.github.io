---
published: false
---
In this post we are going to create a Jenkins machine, instead of starting from the very scratch, we are going to use the machine we created and hooked up with Wire Guard in this post [link to the post]

**Important note**: You do not need to have WireGuard, I'm using it because I want to have everything secured behind my VPN instead of doing manual IP restrictions in LightSail. WireGuard offers IP Roaming, so that way I don't need to worry about the network I'm in, the communication is always encrypted... You can also install Jenkins in a EC2 instance in your VPC, the instructions are pretty straight forward. We are using Ubuntu as operative system.

## First, Install Java 8
Jenkins runs on top of Java JDK, so we are going to install OpenJDK in our machine.

Add the repository where openjdk lives on and update your apt-get:
```
sudo add-apt-repository ppa:openjdk-r/ppa
sudo apt-get update
```

Install Java JDK 8
```
sudo apt-get install openjdk-8-jdk
```

## Jenkins Instalation
We are going to use the WAR versio of Jenkins because it is very easy to set up:

Download Jenkins War
```
wget http://mirrors.jenkins-ci.org/war/latest/jenkins.war
```

Run Jenkins WAR
```
java -jar jenkins.war httpPort=8080
```

Now, Jenkins will start setting up, the first time ever you run it will show the Admin password:

```
*************************************************************
*************************************************************
*************************************************************

Jenkins initial setup is required. An admin user has been created and a password generated.
Please use the following password to proceed to installation:

<THE PASSWORD>

This may also be found at: /home/ubuntu/.jenkins/secrets/initialAdminPassword

*************************************************************
*************************************************************
*************************************************************
```

You allways have it accesible in `/home/ubuntu/.jenkins/secrets/initialAdminPassword`


Now you can go to your favourite Web Browser and go to this address:

```
http://10.0.100.60:8080/
```

***Obvious consideration here:*** If you did not follow the WireGuard set up in the instance or you do not have it, you will need to go to LightSail, and add a connection rule for port 8080 and access something like `http://PUBLIC_IP:8080`

Once you insert your administrative password a screen will show you the installation options for customize Jenkins:


From this point all will be really up to you, depending in your needs, you can add the plugins later on, so I will for the *Install suggested plugins*

Once the plugins are installed you will be asked to create your personal admin user, and then all is ready to go!



