---
templateKey: blog-post
title: Alpine-Hugo-Git-Bash Docker Image
date: 2016-09-25T11:15:00.000Z
featuredimage: /img/docker-alpine-hugo-git-bash_canvas.jpg
tags:
  - docker alpine wercker hugo
---
[![](https://images.microbadger.com/badges/image/andthensome/alpine-hugo-git-bash.svg)](https://microbadger.com/images/andthensome/alpine-hugo-surge-git-bash "Get your own image badge on microbadger.com") [![](https://images.microbadger.com/badges/version/andthensome/alpine-hugo-surge-git-bash.svg)](https://microbadger.com/images/andthensome/alpine-hugo-git-bash "Get your own version badge on microbadger.com")​

I use [wercker](http://wercker.com) to automatically deploy this site. One of the nice things about wercker is that it allows for you use [docker](http://docker.com) images in your builds. To keep build time to a minimum I've created a [docker image](https://hub.docker.com/r/andthensome/alpine-hugo-git-bash/) with all the tools I need to build this site. Namely:

- [Hugo](https://gohugo.io)
- Bash (included bash to ensure compatibility with [available wercker steps](https://app.wercker.com/explore/steps))
- Git

The [Dockerfile](https://github.com/alrayyes/docker-alpine-hugo-git-bash/blob/master/Dockerfile) itself is nothing shocking:

```docker
FROM alpine:latest
MAINTAINER Ryan Kes <ryan@andthensome.nl>

ENV HUGO_VERSION 0.16
ENV HUGO_BINARY hugo_${HUGO_VERSION}_linux-64bit

# Install pygments (for syntax highlighting)
RUN apk update && apk add py-pygments && apk add git && apk add bash && rm -rf /var/cache/apk/*

# Download and Install hugo
ADD https://github.com/spf13/hugo/releases/download/v${HUGO_VERSION}/${HUGO_BINARY}.tgz /usr/local/
RUN tar xzf /usr/local/${HUGO_BINARY}.tgz -C /usr/local/bin/ \
	&& rm /usr/local/${HUGO_BINARY}.tgz
```

A comparison of image sizes & build speeds :

| Image | Size | Build Duration
|--------|------|----------------
| [nodesource/trusty](https://hub.docker.com/r/nodesource/trusty/) | 62.8 MB | 55s
| [andthensome/alpine-hugo-git-bash](https://hub.docker.com/r/andthensome/alpine-hugo-git-bash/) | 33.4 MB | 29s
