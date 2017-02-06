---
title: Why Isn’t OGG Video Playing in Firefox from Amazon S3? It might be all in your headers.
date: 2010-09-27 13:26:54
layout: post
url: http://supergeekery.com/geekblog/comments/why_isnt_ogg_video_playing_in_firefox_from_amazon_s3
featuredImage: transmit_amazon_s3_headers.png
---
Let’s assume you’re trying to build a site with HTML5 video playback and you use Amazon S3\. Good so far, right? Well, depending on how uploaded your video files to your Amazon S3 server, you might end up with video that won’t play back in Firefox. Actually, it’s not that it won’t load, it won’t even load and _attempt_ to play.

It has to do with the headers that are set of the file during their upload. I normally use [Transmit](http://www.panic.com/transmit/ "Learn about Transmit"), the wonderful FTP program for the Mac. If you do the same though, you’re likely to hit the same roadblock I hit. The .ogv video file you’re storing on Amazon S3 or Amazon Cloudfront just doesn’t load into Firefox.

Transmit (as of version 4.1.2, at least) sets the headers of .ogv files to “binary/octet-stream” by default. Unfortunately, that will probably lead to the files not loading when you need them in Firefox. Your beautiful HTML 5 video solution will fail silently.

Transmit has a the ability to set custom headers for files being sent to Amazon S3 though, and if you configure your preferences correctly, your OGG video will work. The image at the beginning of this article shows what your finished preference panel should look like.

First click the left most **+** button and type “ogv” then, with that “ogv” highlighted, click the 2nd **+** button in the bottom center. Under name put “Content-Type” and under value put “video/ogg”. Simple? Maybe, but it’s not very intuitive, but it’s what works.

I only figured this out because I also happen to be using [S3Fox Organizer](http://www.s3fox.net/ "Learn about S3Fox Organizer"), a free Firefox add-on that I also recommend. I had some videos playing in Firefox and some that weren’t. The ones from S3Fox Organizer were playing and the Transmit uploaded files weren’t. It turns out S3Fox Organizer set the content headers for my OGG to “video/ogg” by default. Maybe Transmit will do that by default someday too. (I’ve spoken to a member of the Panic team responsible for Transmit via email and they’ve added it to the feature requests list.) I’m not sure what the reasoning was for setting it to “_binary/octet-stream_” was, but there must have been a reason. _The mysteries of life…_

If you’re on Windows, be sure to check out [CloubBerry](http://www.cloudberrylab.com/default.aspx?page=cloudberry-explorer-amazon-s3 "Learn about CloudBerry"). It also lets you set content headers of files. It’s the only tool of these 3 that I can seem to change the header MIME types of files I’ve _already_ uploaded.
