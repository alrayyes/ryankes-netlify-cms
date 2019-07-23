---
templateKey: blog-post
title: How To Integrate Instagram Into Your Hugo Website
date: 2016-10-01T14:16:00.000Z
description: >
  This is a quick tutorial to teach you how to integrate your Instagram feed
  into your hugo site. To keep things nice and simple I've created a demo theme
  to get you started.
featuredpost: false
featuredimage: /img/integrating-instagram-into-your-hugo-site_header.jpg
tags:
  - hugo instagram api tutorial
---

~~This is a quick tutorial to teach you how to integrate your [Instagram feed](http://instagram.com/alrayyes) into your hugo site~~ [^1]. To keep things nice and simple I've created a [demo theme](https://github.com/alrayyes/instagram-hugo-demo-theme) to get you started, which you can see running [here](https://instagram-hugo-demo.ryankes.eu/).

The demo is basically a one page template. I've highlighted the important part.​

```html{31-43}{numberLines: true}
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="author" content="Ryan Kes" />
    <meta name="generator" content="Hugo 0.16" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta
      name="description"
      content="A page that demonstrates Hugo working with the Instagram api"
    />
    <meta name="keywords" content="Instagram, Hugo, api, tutorial, demo" />
    <meta name="generation-date" content="{{ .Now }}" />

    <title>Instagram Hugo Demo</title>
    <link href="/css/bootstrap.min.css" rel="stylesheet" id="bootstrap-css" />
    <link rel="stylesheet" href="/css/style.css" type="text/css" />
    {{ template "_internal/google_analytics_async.html" . }}
  </head>

  <body>
    <a href="https://github.com/alrayyes/instagram-hugo-demo-theme"
      ><img
        style="position: absolute; top: 0; right: 0; border: 0;"
        src="https://camo.githubusercontent.com/e7bbb0521b397edbd5fe43e7f760759336b5e05f/68747470733a2f2f73332e616d617a6f6e6177732e636f6d2f6769746875622f726962626f6e732f666f726b6d655f72696768745f677265656e5f3030373230302e706e67"
        alt="Fork me on GitHub"
        data-canonical-src="https://s3.amazonaws.com/github/ribbons/forkme_right_green_007200.png"
    /></a>
    <div class="container-fluid">
      <div class="row text-center">
        <h3 style="color:white;font-family:verdana;">
          <a href="https://www.instagram.com/" target="_new">Instagram</a>
          <a href="https://gohugo.io/" target="_new">Hugo</a> Demo<br /><small
            ><a href="http://ryankes.eu">Ryan Kes</a></small
          ><br />
          Design inspired by
          <a
            href="http://bootsnipp.com/snippets/featured/image-tiles-full-width"
            target="_new"
            >Snap TightImage Tiles snippet</a
          ><br />
          See
          <a
            href="https://ryankes.eu/post/integrating-instagram-into-your-hugo-site/"
            >blog post</a
          >
          for more information
        </h3>
      </div>
      <div class="row">
        {{ $dataJ := getJSON (printf
        "https://api.instagram.com/v1/users/self/media/recent/?access_token=%s"
        .Site.Params.access_token) }} {{ range $dataJ.data }}
        <a href="{{ .link }}" target="_new">
          <div
            class="cover-card col-sm-4"
            style="background: url({{ .images.standard_resolution.url }}) no-repeat center top;background-size:cover;"
          >
            <p>
              {{ with .caption }}{{ .text }}<br />
              -<br />{{ end }}
              <span class="play-date" data-date="{{ .created_time }}"></span>
            </p>
          </div>
        </a>
        {{ end }}
      </div>
    </div>

    <script src="/js/jquery-1.10.2.min.js"></script>
    <script src="/js/bootstrap.min.js"></script>
    <script src="/js/moment.min.js"></script>
    <script>
      $(document).ready(function() {
        $("span.play-date").each(function(i, e) {
          var date = moment.unix($(e).data("date"));
          $(e).html(date.fromNow());
        });
      });
    </script>
  </body>
</html>
```

On line 31 we connect to the [Instagram api](https://www.instagram.com/developer/) with 1 parameter:

1. access token (which you can generate [here](http://instagram.pixelunion.net/))

This returns something like:

```json
{
  "pagination": {
    "next_url": "https://api.instagram.com/v1/users/30589186/media/recent?access_token=30589186.1677ed0.04f23d0cd29246d692e1f240b0fd836a\u0026count=1\u0026max_id=1352196774743616727_30589186",
    "next_max_id": "1352196774743616727_30589186"
  },
  "meta": {
    "code": 200
  },
  "data": [
    {
      "attribution": null,
      "tags": ["test", "hugo", "wercker", "ifttt"],
      "type": "image",
      "location": null,
      "comments": {
        "count": 0
      },
      "filter": "Amaro",
      "created_time": "1475414438",
      "link": "https://www.instagram.com/p/BLD981pBazX/",
      "likes": {
        "count": 3
      },
      "images": {
        "low_resolution": {
          "url": "https://scontent.cdninstagram.com/t51.2885-15/s320x320/e35/14488341_1365561496789352_8898975373191020544_n.jpg?ig_cache_key=MTM1MjE5Njc3NDc0MzYxNjcyNw%3D%3D.2",
          "width": 320,
          "height": 320
        },
        "thumbnail": {
          "url": "https://scontent.cdninstagram.com/t51.2885-15/s150x150/e35/14488341_1365561496789352_8898975373191020544_n.jpg?ig_cache_key=MTM1MjE5Njc3NDc0MzYxNjcyNw%3D%3D.2",
          "width": 150,
          "height": 150
        },
        "standard_resolution": {
          "url": "https://scontent.cdninstagram.com/t51.2885-15/s640x640/sh0.08/e35/14488341_1365561496789352_8898975373191020544_n.jpg?ig_cache_key=MTM1MjE5Njc3NDc0MzYxNjcyNw%3D%3D.2",
          "width": 640,
          "height": 640
        }
      },
      "users_in_photo": [],
      "caption": {
        "created_time": "1475414438",
        "text": "Which one to pick? #test #ifttt #wercker #hugo",
        "from": {
          "username": "alrayyes",
          "profile_picture": "https://scontent.cdninstagram.com/t51.2885-19/11821796_875675509184288_365567230_a.jpg",
          "id": "30589186",
          "full_name": ""
        },
        "id": "17842838194149837"
      },
      "user_has_liked": false,
      "id": "1352196774743616727_30589186",
      "user": {
        "username": "alrayyes",
        "profile_picture": "https://scontent.cdninstagram.com/t51.2885-19/11821796_875675509184288_365567230_a.jpg",
        "id": "30589186",
        "full_name": ""
      }
    }
  ]
}
```

Lines 32 - 43 basically just loop through this json and output corresponding html.

The rest of the html is basically styling and using [momentjs](http://momentjs.com/) to generate pretty human readable datetimes. There is one small caveat though, as the generated site is static you need to rebuild it sporadically to keep the scrobbles up to date. I accomplish this using ~~[a cron job to trigger wercker]({{< ref "/posts/how-to-trigger-wercker-builds-with-the-wercker-api.md" >}})~~ [ifttt to trigger a build every time I upload a photo]({{< ref "/posts/how-to-trigger-wercker-builds-with-ifttt.md" >}}).

That's all there is to it. The same principle can also be used to [integrate Last.fm into your site]({{< ref "/posts/integrating-lastfm-into-your-hugo-site.md" >}}). Hopefully this tutorial was clear and concise enough. If not please let me know in the comments below.

[^1]: You really shouldn't be using products [owned by Facebook](https://www.stopusingfacebook.co/). Personally I don't use [Instagram](https://www.instagram.com) anymore, and therefore don't have a working demo to show. At the time of writing Facebook is [closing down the Instagram API](https://techcrunch.com/2018/04/04/facebook-instagram-api-shut-down/) so all of this probably won't work in the not too distant future.​
