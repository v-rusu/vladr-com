---
layout: base.njk
title: "Building This Site With Eleventy"
description: "A quick look at what I learned setting up this blog using Eleventy with Nunjucks"
date: 2026-02-06
tags: post
---

<article>

# {{ title }}
<time datetime="{{ date | dateFormat('iso') }}">{{ date | dateFormat }}</time>

Every blog needs a first post, so I guess it's only fair to talk a bit about what made me go with [Eleventy](https://www.11ty.dev/docs/), instead of any of the alternatives. 

From the start I wanted something where I could write markdown, and push to github to publish. So I could use my favorite IDE to write, and publish using known tools.

## Why Eleventy?

- No client side JS by default
- Flexible templating through Nunjucks and Markdown
- Fast build is a nice addon, but not something that I was interested in particular
- Astro has a purple homepage

## How does Eleventy work

I found this to be in particular interesting. Eleventy is a static site generator. It is not interested in serving the website, it's only interest is generating static HTMLs. 

### Templating

I chose to go with [Nunjucks](https://mozilla.github.io/nunjucks/) as the templating engine. It is the default in Eleventy.  Nunjucks brings variables and functions (they call them filters) to the templates. 

This is the date formatter function I have. 

```javascript
export default function(eleventyConfig) {
  eleventyConfig.addFilter("dateFormat", (date, format) => {
    const d = new Date(date);
    const months = [
      "January", "February", "March", "April", "May", "June",
      "July", "August", "September", "October", "November", "December",
    ];
    if (format === "iso") return d.toISOString().split("T")[0];
    return `${months[d.getMonth()]} ${String(d.getDate()).padStart(2, "0")}, ${d.getFullYear()}`;
  });
};
```

And this is how I use it

```html
<time datetime="{{ date | dateFormat('iso') }}">{{ date | dateFormat }}</time>
```

### Front Matter data

Front Matter is a way to add metadata and other variables to the content pages. Defaults to YAML format. Super easy to use and understand. Has to be added at the top of the page. 

```yaml
---
layout: base.njk
title: "Building This Site With Eleventy"
description: "A quick look at what I learned setting up this blog using Eleventy with Nunjucks"
date: 2026-02-06
tags: post
---
```

Afterwards, the variables defined here are also available in the templates. For example the date I mentioned above is the one declared in this yaml.

## Deployment

In the interest of saving time to get this blog online, I'm publishing the first version on GitHub Pages. In the end this will live in my kubernetes cluster, once I get to setting that up. 

I found the following deployment script in a [github issue](https://github.com/11ty/eleventy/discussions/1455), and I hope it works first try (if you read this, it worked.)

{% raw %}
```yaml
name: build 11ty site

on:
  push:
    branches: ["main"]

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
      - name: Install dependencies & build
        run: |
          npm install
          npx @11ty/eleventy
      - uses: actions/upload-pages-artifact@v4

  deploy:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
```
{% endraw %}

## What's next

I'll be writing about things I'm working on, problems I've solved, and tools I find useful.

</article>
