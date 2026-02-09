# vladr.com

My personal site and blog. Built with [Eleventy](https://www.11ty.dev/) and plain CSS. Write in markdown, push to GitHub, done.

## Running locally

```
npm install
npm run dev
```

## Writing a new post

Add a markdown file to `src/blog/posts/` with frontmatter:

```yaml
---
layout: base.njk
title: "Post Title"
description: "Short description"
date: 2026-02-06
tags: post
---
```

## Deploying

Push to `main`. GitHub Actions builds the site and deploys to GitHub Pages.
