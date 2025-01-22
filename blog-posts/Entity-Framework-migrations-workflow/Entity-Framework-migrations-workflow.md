---
published: false
title: 'Adding Entity Framework migrations to a GitHub workflow'
cover_image: 'https://raw.githubusercontent.com/sn0w-m0nkey/dev.to/refs/heads/master/blog-posts/Entity-Framework-migrations-workflow/assets/GitHub_Logo_Banner.png'
description: ''
tags: githubactions, entityframework, migrations, azure
series: 'GitHub workflows'
canonical_url:
---

https://chatgpt.com/c/67863973-cc84-8001-8fb7-fa3cfff484ad

https://www.dandoescode.com/blog/azure-sql-databases-deploying-updates-with-ef-core-and-github
https://learn.microsoft.com/en-us/ef/core/managing-schemas/migrations/applying?tabs=dotnet-core-cli
https://www.twilio.com/en-us/blog/migrate-your-database-using-entity-framework-core-migration-bundles

This is an example of how to structure a blog post.

The thing above delimited by `---` is called a "front matter" and it allows us to keep control over our article in a very easy way. Just edit it with your own data and CI will handle the rest to publish it to dev.to!

You can also take advantage of [embedme](https://github.com/zakhenry/embedme) to extract your code from the markdown file and make sure that what you're displaying in the markdown is always up to date too e.g.

```ts
// code/demo-code.ts

interface A {
  hello: string;
}
```

# Found a typo?

If you've found a typo, a sentence that could be improved or anything else that should be updated on this blog post, you can access it through a git repository and make a pull request. Instead of posting a comment, please go directly to <REPO URL> and open a new pull request with your changes.
