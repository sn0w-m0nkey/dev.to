---
published: false
title: 'Deleting all artifacts from a GitHub repository'
cover_image: 'https://raw.githubusercontent.com/sn0w-m0nkey/dev.to/refs/heads/master/blog-posts/Deleting-all-artifacts-from-a-GitHub-repository/assets/GitHub_Logo_Banner.png'
description: 'Description of the article'
tags: githubactions, github, artifacts
series: 'GitHub workflows'
canonical_url:
---

Recently after playing around with workflows and running a LOT of them, GitHub told me I'd run out of space and couldn't create any more artifacts, so my workflows wouldn't run.

## Prerequisites

- Create a Personal Access Token in GitHub (PAT)
  - Click on your logo in the top right => **Developer Settings** => **Personal access tokens** => **Tokens** => **Generate new token**
  - Name your token `DELETE_ARTIFACTS_TOKEN`
  - You don't need to give your token any permissions


## Delete Artifacts

Add the **delete-artifacts.yml** file below to the  **.github/workflows** folder of your project.

When you run it manually from GitHub, navigate into the **workflow run** then into the job details, and follow the instructions to authenticate with GitHub CLI. You should be seeing something like this:

![Authenticate with GitHub CLI](./assets/authenticate.png 'Authenticate with GitHub CLI')


## delete-artifacts.yml

```
name: delete artifacts

on:
  workflow_dispatch:

jobs:
  delete-artifacts:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repository (required for GitHub CLI actions)
      - name: Checkout
        uses: actions/checkout@v3

      # Install GitHub CLI
      - name: Install GitHub CLI
        run: |
          sudo apt-get update
          sudo apt-get install -y gh

      # Authenticate GitHub CLI
      - name: Authenticate with GitHub CLI
        env:
          GH_TOKEN: ${{ secrets.DELETE_ARTIFACTS_TOKEN }}
        run: |
          echo "${GH_TOKEN}" | gh auth login --with-token

      # Delete all artifacts
      - name: Delete All Artifacts
        env:
          REPO: ${{ github.repository }}
        run: |
          echo "Fetching artifacts for repository $REPO..."
          gh api -X GET "repos/$REPO/actions/artifacts" --paginate \
            | jq -r '.artifacts[] | .id' \
            | while read -r artifact_id; do
                echo "Deleting artifact $artifact_id..."
                gh api -X DELETE "repos/$REPO/actions/artifacts/$artifact_id"
              done
```
