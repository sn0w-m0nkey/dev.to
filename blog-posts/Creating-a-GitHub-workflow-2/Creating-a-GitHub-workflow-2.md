---
published: false
title: 'Creating a GitHub workflow to deploy a .NET Core app to an Azure App Service - Part 2: Adding Testing'
cover_image: 'https://raw.githubusercontent.com/YOUR-USERNAME/YOUR-REPO/master/blog-posts/NAME-OF-YOUR-BLOG-POST/assets/your-asset.png'
description: 'Description of the article'
tags: githubactinos, netcore, github, azure
series:
canonical_url:
---

As soon as I finished the basic workflow in Part 1, I wanted to learn more so I immediately began working on adding testing. I then separated the code into different jobs (or methods) to make it more readable and to group relevant code together. It also means the separate jobs could be put into separate files to make them reusable if required.