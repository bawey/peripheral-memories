---
title: "Hello World with Hugo"
date: 2023-06-04T11:21:44+02:00
draft: false
tags: [hugo]
---

There's a good [quickstart](https://gohugo.io/getting-started/quick-start/) on the matter but a stranger to go and hugo
I had to resort to some trial-and-error. So here's what worked out.

## First things first...
 
```bash
sudo apt-get install golang hugo
hugo new site scribblings
```

## Installing a theme

I picked [relearn](https://mcshelby.github.io/hugo-theme-relearn/basics/installation/index.html), 
necessitating the following changes to `.toml`: 

```toml
[module]
[[module.imports]]
  path = 'github.com/McShelby/hugo-theme-relearn'
```

At this point it became [apparent](https://mcshelby.github.io/hugo-theme-relearn/basics/migration/) that either I 
upgrade hugo (was at `hugo v0.92.2+extended`) or I downgrade the template. I went with the latter:

```bash
hugo mod get github.com/McShelby/hugo-theme-relearn@3.1.0
```

At this point the server would already start fine: 

```bash
hugo server -D  
```

### Enabling search

For the theme to correctly output a JSON index file, the following addition to the `config.toml` was needed:
```toml
[outputs]
home = [ "HTML", "JSON"]
```

It's pretty much what the [setup guide](https://mcshelby.github.io/hugo-theme-relearn/basics/installation/index.html) 
suggests, barring the `JSON` instead of `SEARCH` format (I guess I should have installed a newer hugo, shouldn't I?). 

Anyway, from here on out it was just a matter of adding one page (this very page, actually) to verify that the search 
is working:
```bash
hugo new posts/hello-world-with-hugo.md
```

Besides, it seems that using a module one no longer needs to declare the active t