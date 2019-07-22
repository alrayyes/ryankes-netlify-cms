---
templateKey: blog-post
title: ' Alpine-Hugo-Surge.sh-Git-Bash Docker Image'
date: 2016-09-22T17:44:00.000Z
featuredimage: /img/docker-alpine-hugo-surge-git-bash_canvas.jpg
tags:
  - docker alpine wercker hugo surge.sh
---
[![](https://images.microbadger.com/badges/image/andthensome/alpine-hugo-surge-git-bash.svg)](https://microbadger.com/images/andthensome/alpine-hugo-surge-git-bash "Get your own image badge on microbadger.com") [![](https://images.microbadger.com/badges/version/andthensome/alpine-hugo-surge-git-bash.svg)](https://microbadger.com/images/andthensome/alpine-hugo-surge-git-bash "Get your own version badge on microbadger.com")

I use [wercker](http://wercker.com) to automatically deploy this site. One of the nice things about wercker is that it allows for you use [docker](http://docker.com) images in your builds. To keep deployment time to a minimum I've created a [docker image](https://hub.docker.com/r/andthensome/alpine-hugo-surge-git-bash/) with all the tools I need to build and deploy this site. Namely:

- [Hugo](https://gohugo.io)
- [Surge.sh client](https://www.npmjs.com/package/surge)
- Bash (included bash to ensure compatibility with [available wercker steps](https://app.wercker.com/explore/steps))
- Git

The [Dockerfile](https://github.com/alrayyes/docker-alpine-hugo-surge-git-bash/blob/master/Dockerfile) itself is nothing shocking:

{{< highlight docker >}}
FROM mhart/alpine-node:latest
MAINTAINER Ryan Kes <ryan@andthensome.nl>

ENV HUGO_VERSION 0.16
ENV HUGO_BINARY hugo_${HUGO_VERSION}_linux-64bit

# Install pygments (for syntax highlighting)
RUN apk update && apk add py-pygments && apk add git && apk add bash && rm -rf /var/cache/apk/*

# Download and Install hugo
ADD https://github.com/spf13/hugo/releases/download/v${HUGO_VERSION}/${HUGO_BINARY}.tgz /usr/local/
RUN tar xzf /usr/local/${HUGO_BINARY}.tgz -C /usr/local/bin/ \
	&& rm /usr/local/${HUGO_BINARY}.tgz

# Install surge client
RUN npm install -g surge
{{< /highlight >}}

A comparison of image sizes & build/deploy speeds :

| Image | Size | Build Duration | Deploy Duration | Total Duration
--------|------|----------------|-----------------|------------------------------
| [nodesource/trusty](https://hub.docker.com/r/nodesource/trusty/) | 62.8 MB | 55s | 33s | 1m28s
| [andthensome/alpine-hugo-surge-git-bash](https://hub.docker.com/r/andthensome/alpine-hugo-surge-git-bash/) | 49.9 MB | 25s | 26s | 51s

While 37 seconds per deployment saved might not sound like a huge deal, every second does count when it comes to continuous automated deployment (Wercker, you're welcome).
