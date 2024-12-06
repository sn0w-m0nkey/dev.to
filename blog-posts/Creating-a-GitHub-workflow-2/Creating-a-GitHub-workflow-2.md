---
published: false
title: 'Creating a GitHub workflow to deploy a .NET Core app to an Azure App Service - Part 2: Adding Testing'
cover_image: 'https://raw.githubusercontent.com/YOUR-USERNAME/YOUR-REPO/master/blog-posts/NAME-OF-YOUR-BLOG-POST/assets/your-asset.png'
description: 'Description of the article'
tags: githubactions, netcore, github, azure
series:
canonical_url:
---

[Part 1](INSERT_LINK_ HERE) TODO
Part 2

As soon as I finished the basic workflow in [Part 1](INSERT_LINK_HERE) TODO, I wanted to learn more so I immediately began working on adding testing. I then separated the code into different jobs (or methods, or function chaining in Azure terms) to make it more readable and also to group relevant code together. It also means the separate jobs could be put into separate files to make them reusable and reduce conflicts if worked on by different people if required.

## New Workflow

Create a new job copying everything from [Part 1](INSERT_LINK_HERE) TODO above and including `jobs` to my new workflow, except for the `name:` which you can change to anything you want.

## Creating Steps

Creat three different steps: build, tests & deploy.

Change `build-and-deploy:` to `build:` for the first job, and then add two more jobs below it called `tests` and `deploy`.

By doing this, instead of getting a simple workflow loking like this in GitHub actions:
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/77ikef2jsy1l2uachl4h.png)

You'll get something a little more detailed like this:
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/lk89wd7jjjm8v8yyab5z.png)

Which means you'll instantly be able to see which part of a process an error occurred or where your workflow problems are.

Add the following under each **Job**

```
    runs-on: ubuntu-latest

    steps:
```


## Build Job

#### Fetch the code from your repository from 

Add the following code  under the **build** `step:`. 


```
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up .NET Core
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '8.x'
```

#### Build 2

#### Build 3


### Tests Job

#### Tests 1

#### Tests 2

#### Tests 3


### Deploy Job

#### Deploy 1

#### Deploy 2

#### Deploy 3


### The end result
