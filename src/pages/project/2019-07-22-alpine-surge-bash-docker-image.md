---
templateKey: blog-post
title: Alpine-Surge-Bash Docker Image
date: 2016-09-25T11:25:00.000Z
featuredimage: /img/docker-alpine-surge-bash_canvas.jpg
tags:
  - docker alpine wercker surge.sh
---
[![](https://images.microbadger.com/badges/image/andthensome/alpine-surge-bash.svg)](https://microbadger.com/images/andthensome/alpine-surge-bash "Get your own image badge on microbadger.com") [![](https://images.microbadger.com/badges/version/andthensome/alpine-hugo-surge-git-bash.svg)](https://microbadger.com/images/andthensome/alpine-hugo-git-bash "Get your own version badge on microbadger.com")​

I use [wercker](http://wercker.com) to automatically deploy this site. One of the nice things about wercker is that it allows for you use [docker](http://docker.com) images in your builds. To keep deployment time to a minimum I've created a [docker image](https://hub.docker.com/r/andthensome/alpine-surge-bash/) with all the tools I need to deploy this site. Namely:

- [Surge.sh client](https://www.npmjs.com/package/surge)
- Bash (included bash to ensure compatibility with [available wercker steps](https://app.wercker.com/explore/steps))
- Git

The [Dockerfile](https://github.com/alrayyes/docker-alpine-surge-bash/blob/master/Dockerfile) itself is nothing shocking:

```docker
FROM mhart/alpine-node:latest
MAINTAINER Ryan Kes <ryan@andthensome.nl>
 
# Install pygments (for syntax highlighting)
RUN apk update && apk add bash && rm -rf /var/cache/apk/*
 
# Install surge client
RUN npm install -g surge
```

A comparison of image sizes & deploy speeds :

| Image | Size | Deploy Duration
|--------|------|----------------
| [nodesource/trusty](https://hub.docker.com/r/nodesource/trusty/) | 62.8 MB | 33s
| [andthensome/alpine-surge-bash](https://hub.docker.com/r/andthensome/alpine-surge-bash/) | 19.8 MB | 16s
