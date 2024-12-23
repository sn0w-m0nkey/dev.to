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

If you feel some explanation is missing from a code block, check [Part 1](INSERT_LINK_HERE) TODO (or comment) as I'm not going to repeat myself and possibly end up having to change in 2 places. Some of **build** is new, **tests** is obviously all new*, and some of **deploy** is new.

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

#### Fetch the code from your repository

Add the following code from [Part 1](INSERT_LINK_HERE) under the **build** step.

```
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up .NET Core
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '8.x'
```

#### Set up dependancy caching

This **step** is new. It caches the nuget dependencies required for multiple steps to be cached to speed things up.

```
      - name: Set up dependency caching for faster builds
        uses: actions/cache@v4
        with:
          path: ~/.nuget/packages
          key: ${{ runner.os }}-nuget-${{ hashFiles('**/packages.lock.json') }}
          restore-keys: |
            ${{ runner.os }}-nuget-
```

#### Restore dependencies, build the project

```
      - name: Restore dependencies
        run: dotnet restore

      - name: Build the project
        run: dotnet publish -c Release -o ./publish
```

#### Upload Artifact

This **step** is also new. 
It saves the published built so that it can be used by other steps without checking it out and downloading it, restoring dependencies, and building it all over agian.

```
      - name: Upload artifact for deployment job
        uses: actions/upload-artifact@v4
        with:
          name: .net-app
          path: ./publish
```

### Tests Job

#### Tests 1

Update the

```
    runs-on: ubuntu-latest
    needs: build
    permissions: write-all
```

#### Tests 2

#### Tests 3


### Deploy Job

#### Deploy 1

#### Deploy 2

#### Deploy 3


### The end result
