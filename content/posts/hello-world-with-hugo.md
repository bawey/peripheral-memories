---
title: "Hello World with Hugo"
date: 2023-06-04T11:21:44+02:00
draft: false
tags: [hugo]
---

There's a good [quickstart](https://gohugo.io/getting-started/quick-start/) on the matter but knowing neither Go nor its
ecosystem it's not entirely impossible to wander off the path (yes it's me I'm talking about).  
The first time around I went with off-the-shelf (i.e., `apt`) packages that proved outdated which in turn triggered some
compatibility issues further down the road. The second take however consisted in compiling `go` from sources and 
installing `hugo` through go rather than with `apt`. 

## Installing hugo
 
### Through a package manager (the quick way - but lacking flexibility)
While `hugo` can be installed directly through `apt`, its version might be a little stale, meaning possible issues with
certain themes later on. Anyway, for a quick setup and test could be done like so:
```bash
sudo apt-get install golang hugo
hugo new site scribblings
``` 

### As a go package (the now-preferred way)
With this strategy, `go` can still be installed through a package manager or by downloading pre-compiled binaries or, 
for more fun, by compiling the latter yourself.

#### Compiling go from sources
- it's documented [here](https://go.dev/doc/install/source#install) but a sense of incongruity crept in pretty soon:
  - `apt` package with specified name (`gccgo-5`) was not found
  - `update-alternatives` came up empty when it probably shouldn't
  - errors that I neither expected nor understood...
- anyway, after some minor adjustments, it seemed to work out like so:
  - have some `go` version that can compile the current checkout
    - I *did* install the `gccgo` (how it differs from `gccgo-5` I have not investigated)
    - ... then created a symlink:  `ln -s /usr/bin/go-11 /tmp/bin/go`
    - ... and used `GOROOT_BOOTSTRAP=/tmp` value so that the `$GOROOT_BOOTSTRAP/bin/go` is a "bin-go" 😬
  - that seemed to do the job up to the version `1.19` - allowing the now-compiled `go` to act as the new bootstrapper:
    - having compiled the above, I copied entire checkout area to `${HOME}/.golang`
      - I'd guess I could've checked it out there but the `GOROOT` and bootstrap location cannot be the same
    - I found out that I had wanted the following in my `.bashrc`:
      - `export GOPATH="${HOME}/.golang"`
      - `export PATH="${PATH}:${GOPATH}/bin"`
        - without the latter, new `go` packages would up under `$HOME/go` (will come handy when installing `hugo`)
      - to test: reload `.bashrc` and see `go version` produce something reasonable
    - I compiled `1.21.3` with `GOROOT_BOOTSTRAP=${HOME}/.golang`
    - I wiped my `GOPATH` directory (just in case) and repopulated it with the checkout area's contents
#### Getting that `hugo` package
Depending on the version of your choosing it should either look exactly like or very close to:
```bash
go install -tags extended github.com/gohugoio/hugo@v0.111.3
```

## Installing a theme

I picked [relearn](https://mcshelby.github.io/hugo-theme-relearn/basics/installation/index.html), 
necessitating the following changes to `.toml`: 

```toml
[module]
[[module.imports]]
  path = 'github.com/McShelby/hugo-theme-relearn'
```

### Compatibility issues
This is where the pre-compiled `hugo` distribution got me into trouble the first time around.
In line with the [release notes](https://mcshelby.github.io/hugo-theme-relearn/basics/migration/) I could either  
upgrade hugo (was at `hugo v0.92.2+extended`) or downgrade the template. 

At the time I went with the latter:
```bash
hugo mod get github.com/McShelby/hugo-theme-relearn@3.1.0
```

At this point the server would already start fine:
```bash
hugo server -D  
```

Which, anecdotally, is also what happened during the second setup mentioned above - and without any downgrading 
whatsoever (but with much newer versions of `go` and `hugo`). 

### Enabling search

For the theme to correctly output a JSON index file, the following addition to the `config.toml` was needed:
```toml
[outputs]
home = [ "HTML", "JSON"]
```

It's pretty much what the [setup guide](https://mcshelby.github.io/hugo-theme-relearn/basics/installation/index.html) 
suggests, barring the `JSON` instead of `SEARCH` format (perhaps that's only relevant to some older versions of `hugo`). 

Anyway, from here on out it was just a matter of adding one page (this very page actually) to verify that the search 
is working:
```bash
hugo new posts/hello-world-with-hugo.md
```

## Aligning the versions
If you get weird errors suggesting that `hugo` is not playing nicely with `go` or that the theme's code is misunderstood, 
consider the following:  
- iterating over the [tags](https://github.com/gohugoio/hugo/tags) in search of the latest that works
  - at some point I found the following: `go install -tags extended github.com/gohugoio/hugo@v0.111.3`
- checking [theme releases for compatibility](https://mcshelby.github.io/hugo-theme-relearn/basics/migration/)
  - at some point it turned out to be (the latest at the moment): `hugo mod get github.com/McShelby/hugo-theme-relearn@5.15.0`
- pinning your local versions to those of your CI/CD setup if you have one and if it's working as intended