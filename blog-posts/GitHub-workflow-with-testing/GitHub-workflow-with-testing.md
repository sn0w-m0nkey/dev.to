---
published: false
title: 'Creating a GitHub workflow for .NET Core and Azure App Services with testing'
cover_image: 'https://raw.githubusercontent.com/sn0w-m0nkey/dev.to/refs/heads/master/blog-posts/GitHub-workflow-with-testing/assets/GitHub_Logo_Banner.png'
description: ''
tags: github, githubactions, netcore, azure
series: 'GitHub workflows'
canonical_url:
---

*For the end result, skip to the bottom of the page.*

If you feel some explanation is missing from a code block, maybe check [Part 1](INSERT_LINK_HERE) TODO.


## New Workflow

Create a new workflow starting from this. Change the name to anything  you want.

```
name: deploy with testing

on:
  push:
    branches:
      - main
  workflow_dispatch:

jobs:
```


## Creating Jobs

Creat three different jobs: `build`, `tests` & `deploy`.

```
jobs:
  build:

  test:

  deploy:
```

By doing this, instead of getting a simple workflow looking like this in GitHub actions:

![Single Job](./assets/single_job.png 'Single Job')

You'll get something a little more detailed like this:

![Multiple Jobs](./assets/multiple_jobs.png 'Multiple Jobs')

Which means you'll instantly be able to see which part of a process an error occurred or where your workflow problems are.

Add the following under each **Job**.

```
    runs-on: ubuntu-latest
 
    steps:
```

We'll add the steps for each job next.


## Build Job

### Fetch the code from your repository

Add the following code from [Part 1](INSERT_LINK_HERE) as the first **build** step.

```
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up .NET Core
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '8.x'
```


### Set up dependancy caching

Cache dependencies or build outputs to speed up future workflow runs.

```
      - name: Set up dependency caching for faster builds
        uses: actions/cache@v4
        with:
          path: ~/.nuget/packages
          key: ${{ runner.os }}-nuget-${{ hashFiles('**/packages.lock.json') }}
          restore-keys: |
            ${{ runner.os }}-nuget-
```


### Restore dependencies, build the project

This step was in [Part 1](INSERT_LINK_HERE).

```
      - name: Restore dependencies
        run: dotnet restore

      - name: Build the project
        run: dotnet publish -c Release -o ./publish
```


### Upload Artifact

Save the published build artifact so that it can be used by other steps without checking it out and downloading it, restoring dependencies, and building it all over agian.

```
      - name: Upload artifact for deployment job
        uses: actions/upload-artifact@v4
        with:
          name: .net-app
          path: ./publish
```


## Tests Job

### Update the configuration

In the **tests** job, add the following code to the **configuration** section under the `runs-on: ubuntu-latest` line that you added earlier.

The `needs:` line defines a **job** that is required to end before this job starts. Think of synchronous programming.

The `permissions` line is required to create the test results file which will be saved on GitHub and used for upcoming steps.

```
    runs-on: ubuntu-latest
    needs: build
    permissions: write-all
```


### Download the artifact

Download the built artifact we uploaded in the **build** step, reducing the need to do the whole checkout and build process again.

```
      - name: Download artifact from build job
        uses: actions/download-artifact@v4
        with:
          name: .net-app
```


### Run your tests

Run your test project. Change `Tests.dll` to the name of your test project.

The test results will be saved in a file called `test_results.trx` to be used in upcoming steps.

```
      - name: Run Tests from Artifact
        run: dotnet test Tests.dll --logger "trx;LogFileName=test_results.trx" --results-directory ./TestResults
```


### Upload test results to the workflow run in a zip file

Upload the test results to the workflow run in a zip file that you can download.

This step is optional and you may prefer to use the following step.

```
      - name: Upload test results to the workflow run in a zip file
        uses: actions/upload-artifact@v4 
        if: ${{ always() }} # Always run this step even if tests fail
        with:
          name: test-results
          path: ./TestResults/*.trx
```


### Add test results to the workflow run as a new Job

Add a new job to the workflow run called Test Results so that the tests can be viewed easily and in detail in the workflow run on GitHub.

```
      - name: Add Dorny test results to the workflow run
        uses: dorny/test-reporter@v1
        if: always()  # Always run this step even if tests fail
        with:
          name: Test Results
          artifact: test-results
          path: "**/*.trx"  # Path to the TRX test result files created by 'dotnet test'
          reporter: dotnet-trx  # Use the .NET TRX parser
          fail-on-error: false  # Don't fail the workflow if the report has issues
```

## Deploy Job

### Update the configuration

Under `runs-on: ubuntu-latest`, add `needs: tests`.

```
  deploy:
    runs-on: ubuntu-latest
    needs: tests
```

### Download the artifact

Once again we download the built artifact we uploaded in the **build** step, reducing the need to do the whole checkout and build process again.

```
      - name: Download artifact from build job
        uses: actions/download-artifact@v4
        with:
          name: .net-app
```

### Deploy to Azure

This step needs you to have copied your **publish profile** from Azure to your GitHub secrets. Refer to [Part 1](INSERT_LINK_ HERE).


## The end result

```
name: deploy

on:
  push:
    branches:
      - main
  workflow_dispatch:

jobs:
  build:
    
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up .NET Core
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '8.x'

      - name: Set up dependency caching for faster builds
        uses: actions/cache@v4
        with:
          path: ~/.nuget/packages
          key: ${{ runner.os }}-nuget-${{ hashFiles('**/packages.lock.json') }}
          restore-keys: |
            ${{ runner.os }}-nuget-

      - name: Restore dependencies
        run: dotnet restore

      - name: Build the project
        run: dotnet publish -c Release -o ./publish

      - name: Upload artifact for deployment job
        uses: actions/upload-artifact@v4
        with:
          name: .net-app
          path: ./publish

  tests:
    runs-on: ubuntu-latest
    needs: build
    permissions: write-all

    steps:
      - name: Download artifact from build job
        uses: actions/download-artifact@v4
        with:
          name: .net-app

      - name: Run Tests from Artifact
        run: dotnet test Tests.dll --logger "trx;LogFileName=test_results.trx" --results-directory ./TestResults
      
      - name: Upload test results to the workflow run in a zip file
        uses: actions/upload-artifact@v4 
        if: ${{ always() }} # Always run this step even if tests fail
        with:
          name: test-results
          path: "**/*.trx"
          
      - name: Add Dorny test results to the workflow run
        uses: dorny/test-reporter@v1
        if: always()  # Always run this step even if tests fail
        with:
          name: Test Results
          artifact: test-results
          path: "**/*.trx"  # Path to the TRX test result files created by 'dotnet test'
          reporter: dotnet-trx  # Use the .NET TRX parser
          fail-on-error: false  # Don't fail the workflow if the report has issues

  deploy:
    runs-on: ubuntu-latest
    needs: tests
    
    steps:
      - name: Download artifact from build job
        uses: actions/download-artifact@v4
        with:
          name: .net-app

      - name: Deploy to Azure Web App
        uses: azure/webapps-deploy@v2
        with:
          app-name: 'wa-github-workflows'
          publish-profile: ${{ secrets.AZURE_PUBLISH_PROFILE }}
          package: .
```