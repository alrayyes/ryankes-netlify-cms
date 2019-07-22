---
templateKey: blog-post
title: How To Trigger Wercker Builds With IFTTT
date: 2016-10-02T13:24:00.000Z
description: |
  How to trigger Wercker builds with IFTTT
featuredpost: false
featuredimage: /img/how-to-trigger-wercker-builds-with-ifttt_header.jpg
tags:
  - ifttt wercker tutorial
---
In a previous post I explained how to [trigger Wercker builds using their api]({{< ref "/posts/how-to-trigger-wercker-builds-with-the-wercker-api.md" >}}). Call the api endpoint in a cronjob once in a while and boom you're done. This is swell for cases like my [lastm demo page](https://lastfm-hugo-demo.ryankes.eu/) which gets updated regularly, but what about the [Instagram demo page](https://instagram-hugo-demo.ryankes.eu/)? As I'm not a 15 year old girl I don't feel the need to Instagram my life to the world 24 / 7. Ideally I only want a build to be triggered every time I upload a photo. ​

This is easily done with [IFTTT](https://ifttt.com/).

For those of you that have never heard of ifttt this is from its [Wikipedia entry](https://en.wikipedia.org/wiki/IFTTT):

> IFTTT is a free web-based service that allows users to create chains of simple conditional statements, called "recipes", which are triggered based on changes to other web services such as Gmail, Facebook, Instagram, and Pinterest. FTTT is an abbreviation of "If This Then That".
>  
> An example "recipe" might consist of sending an e-mail message if the IFTTT user tweets using a certain hashtag. Or, if the user is tagged by someone on Facebook, then that photo will be added to the user's cloud-based photo archive.

***

So let's get started then. First get the piplineId & token by following the steps in my [other wercker build trigger tutorial]({{< ref "/posts/how-to-trigger-wercker-builds-with-the-wercker-api.md" >}}). You should end up with a build command like
                                                              
```bash
curl  -H 'Content-Type: application/json' -H  'Authorization: Bearer 7c6a180b36896a0a8c02787eeafb0e4c' -X POST -d '{"pipelineId": "01f995498924d4e3024b52e3941c8468"}' https://app.wercker.com/api/v3/runs/
```

Unfortuantely IFTTT doesn't support request headers. Luckily though with Wercker you can also [pass the authentication token in the body](http://devcenter.wercker.com/api/getting-started/authentication.html), even though they don't recommend it. Problem solved.

***

Now we can create a new recipe to trigger Wercker builds on Instagram updates. It's pretty straight forward:

Choose "Instagram => Any new photo taken by you" as an IF trigger
-
![IFTTT screenshot of Instagram trigger](how-to-trigger-wercker-builds-with-ifttt_instagramtrigger.png)

Choose "Maker => Make a web request" as an action
-
![IFTTT screenshot of Maker action](how-to-trigger-wercker-builds-with-ifttt_makeraction.png)

***

When enabled the recipe should start triggering Wercker builds, that's all there really is to it. IFTTT is one of those wonderful tools where your only real boundry is your imagination. I'm interested to know how you use it, let me know in the comments.
