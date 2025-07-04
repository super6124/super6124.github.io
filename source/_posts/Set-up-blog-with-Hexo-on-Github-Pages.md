---
title: Set up blog with Hexo on Github Pages
date: 2025-07-04 21:21:18
tags:
---

Sometimes you may want to build a blog to record your learning. As a beginner, you may consider using the Hexo framework with Github Pages to quickly set up you blog website.

## Hexo

Hexo is a framework which convert markdown file into html page. Markdown has relatively simplier syntax, which means you do not need to waste time on wrapping your article with tags.

# Setup

You can create a blog website with the following 3 command. Sounds simple right?

``` bash
$ npm install hexo-cli -g   # Install Hexo
$ hexo init blog            # Create blog website
$ cd blog
```

You can create new posts by `hexo new "<Title of you blog post>"`. If you want to see the result, type `hexo s` and check on `localhost:4000`.

## Github Pages

Github Pages is a hosting service provided by GitHub. You can host static website for free by the following steps:

1. Create a repository called `<your github username>.github.io`.
2. Push the files into the repository `main/master` branch.
3. In the repository, go to **Settings > Pages > Source**, change the source to **Github Actions**.
4. Create file `.github/workflows/pages.yml` with the following contents and push to `main/master` (you may need to check your node.js version and fill into the file below):
``` yml
name: Pages

on:
  push:
    branches:
      - main # default branch

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          submodules: recursive
      - name: Use Node.js <your node.js version>
        uses: actions/setup-node@v4
        with:
          node-version: "<your node.js version>"
      - name: Cache NPM dependencies
        uses: actions/cache@v4
        with:
          path: node_modules
          key: ${{ runner.OS }}-npm-cache
          restore-keys: |
            ${{ runner.OS }}-npm-cache
      - name: Install Dependencies
        run: npm install
      - name: Build
        run: npm run build
      - name: Upload Pages artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public
  deploy:
  	needs: build
    permissions:
      pages: write
      id-token: write
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```
5. Done! you have create a blog website on `<your github username>.github.io`.