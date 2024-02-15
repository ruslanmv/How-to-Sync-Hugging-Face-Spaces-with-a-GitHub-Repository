# How to Sync Hugging Face Spaces with a GitHub Repository

## Introduction:
In this blog, we'll walk you through the process of syncing a Hugging Face Space with a GitHub repository. As an example, we'll sync the following GitHub repository: https://github.com/ruslanmv/Milvus-Client-Embedding into a Hugging Face Space: https://huggingface.co/spaces/ruslanmv/Milvus-Client-Embedding.

## Prerequisites:
- A GitHub repository
- A Hugging Face Space
- Hugging Face API token (found under API Tokens on your Hugging Face profile)

## Step 1: Set up your GitHub repository and Spaces app together

First, add your Spaces app as an additional remote to your existing Git repository by running the following command in your terminal:

```bash
git remote add space https://huggingface.co/spaces/HF_USERNAME/SPACE_NAME
```

Replace `HF_USERNAME` with your Hugging Face username and `SPACE_NAME` with your Space name.

For our example, let us enter to your folder where you have your GitHub Repository

```
git clone https://github.com/ruslanmv/Milvus-Client-Embedding 
cd Milvus-Client-Embedding 
```
Then by using  `HF_USERNAME=ruslanmv` and `SPACE_NAME=Milvus-Client-Embedding`, the command would be:

```bash
git remote add space https://huggingface.co/spaces/ruslanmv/Milvus-Client-Embedding
```

## Step 2: Force push to sync everything for the first time

Run the following command in your terminal to sync your GitHub repository and Hugging Face Space:

```bash
git push --force space main
```

## Step 3: Set up a GitHub Action to push your main branch to Spaces
Create a new GitHub Action in your repository with the following content:

```yaml
name: Sync to Hugging Face hub
on:
  push:
    branches: [main]

  # to run this workflow manually from the Actions tab
  workflow_dispatch:

jobs:
  sync-to-hub:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
          lfs: true
      - name: Push to hub
        env:
          HF_TOKEN: ${{ secrets.HF_TOKEN }}
        run: git push https://HF_USERNAME:$HF_TOKEN@huggingface.co/spaces/HF_USERNAME/SPACE_NAME main
```

Replace `HF_USERNAME` with your username and `SPACE_NAME` with your Space name. Then, create a GitHub secret with your `HF_TOKEN`. You can find your Hugging Face API token under API Tokens on your Hugging Face profile.

For our example with `HF_USERNAME=ruslanmv` and `SPACE_NAME=Milvus-Client-Embedding`, the `run` line in the GitHub Action would be:

```yaml
run: git push https://ruslanmv:$HF_TOKEN@huggingface.co/spaces/ruslanmv/Milvus-Client-Embedding main
```

## Step 4: Create an Action
Create an Action that automatically checks the file size of any new pull request
Add another GitHub Action to your repository with the following content:

```yaml
name: Check file size
on:
  pull_request:
    branches: [main]

  # to run this workflow manually from the Actions tab
  workflow_dispatch:

jobs:
  sync-to-hub:
    runs-on: ubuntu-latest
    steps:
      - name: Check large files
        uses: ActionsDesk/lfs-warning@v2.0
        with:
          filesizelimit: 10485760 # this is 10MB
```

This action will automatically check the file size of any new pull request and ensure that no files larger than 10MB are added to the repository.

## Conclusion:

By following these steps, you've successfully synced your GitHub repository with your Hugging Face Space. Now, any changes you push to your GitHub repository will automatically be reflected in your Hugging Face Space. In our specific example, changes pushed to the `ruslanmv/Milvus-Client-Embedding` GitHub repository will be synced with the `ruslanmv/Milvus-Client-Embedding` Hugging Face Space.