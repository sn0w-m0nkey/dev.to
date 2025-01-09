---
published: false
title: 'Creating a basic GitHub workflow for Azure App Services'
cover_image: 'https://raw.githubusercontent.com/sn0w-m0nkey/dev.to/refs/heads/master/blog-posts/Creating-a-GitHub-workflow-1/assets/GitHub_Logo_Banner.png'
description: ''
tags: githubactions, netcore, github, azure
series: 'GitHub workflows'
canonical_url:
--- 

*For the end result, skip to the bottom of the page.*

When learning how to deploy .NET apps to Azure I struggled to find to find a solid example specific to my needs. 

My learning is still in progress and after several messy but still successful attempts, I came up with what you see below. It's an extremely basic example and I intend to build on it in future posts.


## Prerequisites

- Azure Setup:
  - Create an Azure App Service configured for .NET hosting.
  - Obtain the **publish profile** of the App Service:
     - Go to your App Service in the Azure portal.
     - Navigate to **Deployment Center** > Get **Publish Profile**, and download it.
- GitHub Setup:
  - Your GitHub repository should contain your .NET web app source code.
  - Save the App Service **publish profile** as a secret
     - Go to your repo > **Settings** > **Secrets & Variables** > **Actions** > **New Repository Secret**.
     - Name it `AZURE_PUBLISH_PROFILE` and set the value to the content of the publish profile file you downloaded.


## Creating the Workflow

- Create the following directory in the root of your project: `.github/workflows/`
- Create a YAML file inside that directory. It doesn't matter what it's called, as long as has the `.yml` extension, for example: `deploy.yml`.
- Add the following code blocks:


### Give your workflow a name:

```
name: Deploy .NET App to Azure App Service
```


### Define the trigger 
In this example, the workflow is run whenever a push is made to main branch, and workflow_dispatch enables it to be run manually from GitHub. 
Follow this [link](https://docs.github.com/en/actions/writing-workflows/choosing-when-your-workflow-runs/events-that-trigger-workflows) to see all the trigger options you have.

```
on:
  push:
    branches:
      - main
  workflow_dispatch:
```


### Configure the workflow environment

```
jobs:
  build-and-deploy:
    permissions: write-all
    runs-on: ubuntu-latest
```


### Now we start creating the workflow
```
    steps:
```


### Fetch the code from your repository

```
      - name: Checkout codedepen
        uses: actions/checkout@v4
```


### Install .NET Core to build your application

Set the `dotnet-version` to the version you're using.

```
      - name: Set up .NET Core
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '8.x'
```


### Download all dependencies

```
      - name: Restore dependencies
        run: dotnet restore
```


### Publish the application in release mode to the publish folder

`-c` defines the build configuration which in this workflow is `Release`.
`-o` defines the output directory which is `./publish`.
More information on `dotnet publish` can be found [here](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-publish).

```
      - name: Build the project
        run: dotnet publish -c Release -o ./publish
``` 


### Deploy the application using the publish profile you saved earlier

```
      - name: Deploy to Azure Web App
        uses: azure/webapps-deploy@v2
        with:
          app-name: '<your app service name>'
          publish-profile: ${{ secrets.AZURE_PUBLISH_PROFILE }}
          package: ./publish
```


### The end result

```
name: simple deploy

on:
  push:
    branches:
      - main
  workflow_dispatch:

jobs:
  build-and-deploy:
    permissions: write-all
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up .NET Core
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '8.x'

      - name: Restore dependencies
        run: dotnet restore

      - name: Build the project
        run: dotnet publish -c Release -o ./publish

      - name: Deploy to Azure Web App
        uses: azure/webapps-deploy@v2
        with:
          app-name: '<your app service name>'
          publish-profile: ${{ secrets.AZURE_PUBLISH_PROFILE }}
          package: ./publish
```
