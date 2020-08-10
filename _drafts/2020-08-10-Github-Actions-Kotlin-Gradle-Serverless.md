---
published: false
---
## Continuous Integration and Deployment with Github Actions

## What is CI/CD and why it is important?

Continous Integration is the ability to have a non stop building mechanism in our repositories, so basically, when we commit some code, it gets compile, tested and built, apart from other things like Lint Int, test Coverage checks, documentation generation...

Why it is imporatant? Because you can visually see that the code you have in your master branch is kosher, so you can trust it and if you get it you can work on it and you are sure there are no problems. If you have been playing this development game for a while, I'm sure you have been involved in the following scenario:

```
- Ey NAME-DEVELOPER-1, the code doesn't compile
- In my machine it does NAME-DEVELOPER-2
- I don't mind ND1, have you commited all the libraries?
- Ups, I forgot to add this one!
```

This was a pretty quick resolution, I remember we had a White and Black Cowboy Hat in the office that we wear if we f&cked up.

These days, if you have this kind of problems is because you want, you can have CI set up in place, and when someone creates a `pull request` Github won't allow you to merge if the code doesn't build, and then, when you merge into `master` it will build again.

And you can go one step ahead, and do something like, if you merge into `master` then we are going to automatically `deploy`, even production, so that way, you make sure you have properly tested all.

This is called Continuos Deployment, and actually, it could sound scary at first glance, but it is a practice I like and I believe about.

## Github Actions

There are a big bunch of tools for CI/CD, just to name some we have Jenkins (and you can check in this post how to even deploy it for you using a cheap Lightsail instance), TeamCity, Travis, CircleCI (that I actually like a lot) and the one I'm using: Github Actions

Why I'm using GitHub Actions?

Well I think the main reason, is that I have my code in GitHub, and the integration with GitHub actions it is delightful, absolutely Seamless.

They are free for Public Repositories (like the one I'm using for this Example Task) and if your repos are private, you have 2000 minutes of processing monthly for free (and this is actually pretty decent free tier).

They are really EASY to use, specially if you fight a bit and understand how they work.

In the following example we are going to see hoy I set up for our project the `Pull Request` and the `Push to Master` Github Actions.

### Considerations

In GitHubActions you create Workflows, that contains the Jobs and Steps you want to perform.

They are created in the folder `.github/workflows/` and they are basically YAML files.

I won't go into much detail, because the documention is pretty good, but some considerations:

The Jobs are runninng in parallel, so if you have 3 Jobs, they will run in parallel, and you can indicate dependencies between them if you need 1 job to be finised before the others, a good example could be that you are publishing/deploying your program in several environments, so you will need, first of all, to compile, test and build and then you publish in Test and Dev and if they have been deployed well, you deploy into production.

Steps are the different operations that you run inside the Job, and you can understand it like the different things you need to do, for example, you will need to 


### The Pull Request workflow

In order to make sure our Pull Requests are healthy, we are going to make sure the project build, this is the script:

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



### The Push to Master workflow

[Picture of GithbActions]
I have my code in Github, and Github now offers Github Actions, free for Public repositories and a bunch of hours for Private ones.

And they are fantastic, the thing I like the most is that they are already in Github that is something I'm using already and that it is very easy to use, specially if fight for a bit with it and you understand how it works or you read a post like this one [post] where I explain how I set up and configured my project.

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
