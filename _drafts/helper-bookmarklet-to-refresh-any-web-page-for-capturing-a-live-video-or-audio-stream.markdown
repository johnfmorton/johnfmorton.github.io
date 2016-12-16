---
title: Helper bookmarklet to refresh any web page for “capturing” a live video or audio stream.
date: 2014-02-28 11:01:00
layout: post
url: http://supergeekery.com/geekblog/comments/refresh-any-web-page-for-capturing-a-live-stream
---
I was not going to be near my computer for a recent live webcast I really wanted to watch; unfortunately the event was also not being archived by the site. Live viewing was the only option unless I could capture the stream for watching the next day.

Luckily there are a variety of tools for stream capturing. Two that I use and recommend are [Jaksta](http://www.jaksta.com/ "Jaksta home page.") for video and audio and [Audio Hijack Pro](https://www.rogueamoeba.com/audiohijackpro/ "Audio Hijack Pro home page.") for audio only. (For why I use Audio Hijack Pro for audio when Jaksta does that too is addressed at the end of this post.)

Either program will do the media capture, but there was still a problem I needed to overcome. I needed to be at my computer to refresh the web page that was streaming the event so that the stream would actually begin.

## What’s a nerd to do?

Being a tinkerer, I thought there should be a bookmarklet in my toolbar to do this, so I wrote one. (BTW, I’m using **Chrome on a Mac**. With other configurations, your results may vary. Feel free to fork the [Gist](https://gist.github.com/johnfmorton/9238562 "Gist of this code from John Morton on Github.") to tinker with this.)

Before I get any farther, if you just want to bookmarket to auto refresh your page, drag the button below to browser toolbar and get to refreshing.

<a
href="javascript: (function () {  var jsCode = document.createElement('script');  jsCode.setAttribute('src', 'https://gist.githubusercontent.com/johnfmorton/9238562/raw/b2a65b62c17569d3d275b9f2a755d14658e8d036/pagerefresher.js'); document.body.appendChild(jsCode); }());" title='Auto Page Refresher Bookmarklet' style='border: 2px solid #8F8F8F;padding: 10px;margin: 10px 0 0 0;text-decoration: none;'>Refresher</a>

## What’s going on here?

Refreshing a web page is pretty simple to do with Javascript.

	window.location.reload(true);

If you put this into the console of a web page and execute it, you’ll see whatever page you’re on will refresh immediately. All you need to do is wrap that in a setTimeout function to execute in the future and you’re set.

	// reload in 10000 milliseconds, ie 10 seconds
	setTimeout(function() { window.location.reload(true); }, 10000); 

The rest of the code in the bookmarklet are just prompts for input.

	var milliscdsFromNow = prompt("When should this page refresh?");

The javascript is wrapped in a link which you can drag to your browser’s toolbar with other page links. The actual code is a gist on Github here: [https://gist.github.com/johnfmorton/9238562](https://gist.github.com/johnfmorton/9238562 "Gist of this code from John Morton on Github.").

I hope this helps you get your stream on.

## Why 2 streaming capturing piees of software?

I use both Jaksta and Audio Hijack Pro. Jaksta attempts to capture the actual data as it streams in. This is great for video and audio both, but I’ve noticed that this is sometimes causes issues that cause the stream capture to restart mid stream and not do a complete capture. For example, you may end up with only the last 10 minutes of the stream. I’m not sure why this happens, but my guess is that the streaming service has issued some sort of “restart the stream” command somehow and it gets reset.

Audio Hijack Pro captures the stream a bit further down the line. For example it can capture audio from any particular program or from the system. This capture process doesn’t seem to suffer from these mysterious restarts, but you don’t get the option of capturing video.

So, basically, if I’m doing only audio capture, I use Audio Hijack Pro because it’s more reliable. If I’m capturing video Jakska is fantastic in most cases, but the rare “restart of the stream” means there is a chance I won’t actually end up with the entire program.