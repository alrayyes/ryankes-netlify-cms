---
templateKey: blog-post
title: How To Trigger Builds With the Wercker API
date: 2016-09-24T11:50:00.000Z
description: >
  This site is built using [Hugo](https://gohugo.io) with automatic builds &
  deployments handled by [Wercker](http://wercker.com). To keep the [tunes
  section]({{< ref "/tunes.md" >}}) of this site up to date builds are triggered
  using the [Wercker API](http://devcenter.wercker.com/api/). Unfortunately
  decent documentation is a bit lacking when it comes to clarity, so here's a
  little tutorial on how I got this to work. Important stuff in the api return
  json is highlighted.
featuredpost: false
featuredimage: /img/header.jpg
tags:
  - wercker tutorial
---
This site is built using [Hugo](https://gohugo.io) with automatic builds & deployments handled by [Wercker](http://wercker.com). To keep the [tunes section]({{< ref "/tunes.md" >}}) of this site up to date builds are triggered using the [Wercker API](http://devcenter.wercker.com/api/). Unfortunately decent documentation is a bit lacking when it comes to clarity, so here's a little tutorial on how I got this to work. Important stuff in the api return json is highlighted.​
