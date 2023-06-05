---
title: "Hello World with Hugo"
date: 2023-06-04T11:21:44+02:00
draft: false
tags: [hugo]
---

There's a good [quickstart](https://gohugo.io/getting-started/quick-start/) on the matter but not knowing Go nor its
ecosystem I got myself into some trouble. 
If I were to start over I'd skip off-the-shelf `hugo` version (`apt`) but install through `go install` instead. 

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

## Addendum on getting more recent versions of hugo and re-learn to work

- `go version` produced `go version go1.18.1 linux/amd64` - which I assumed to be good enough
- bruteforce through the [tags](https://github.com/gohugoio/hugo/tags) in search of the latest that works
  - found the following: `go install -tags extended github.com/gohugoio/hugo@v0.111.3`
- set the `$PATH` for convenience: `export PATH="${PATH}:/home/bawey/go/bin"`
  - I haven't customized the `GOPATH` or `GOBIN` variables when installing `hugo`
- checked [theme releases for compatibility](https://mcshelby.github.io/hugo-theme-relearn/basics/migration/)
  - it turned out to be the latest at the moment: `hugo mod get github.com/McShelby/hugo-theme-relearn@5.15.0`
- if using something like github actions, it's probably best to align the versions