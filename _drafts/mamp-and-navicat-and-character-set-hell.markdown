---
title: MAMP and Navicat and Character Set Hell.
date: 2012-08-16 12:26:37
layout: post
url: http://supergeekery.com/geekblog/comments/mamp-and-navicat-and-character-set-hell
---
I’ve used Navicat for years. I’ve used MAMP Pro for years. Both tools are must haves as far as I’m concerned, but I had been having problems with special characters being corrupted for nearly 6 months. Every curly quote, m-dash or umlaut was torturous.

Briefly, the problem was that those special characters (like curly quotes) would be changed to question marks in my database after I had migrated it to or from my local development environment. I knew it was a character set problem, but all my setting _seemed_ to be correct.

_Seemed_ is the correct word though. Although my database level character sets were fine, I overlooked an important additional setting. First, I’ll show you what I got right initially.

![database properites in Navicat](/img/database-properties.png)

UTF-8 is what I wanted because I was using ExpressionEngine which uses this character set. In my situation, the online development and production servers both have their databases set to use UTF-8 and my local MAMP didn’t initially. Setting my local databases’ character set was the right thing to do. The problem was I was still having the problem when I transferred my data using Navicat.

It turns out the connection (aka, bookmark) also had it’s own character encoding that I hadn’t updated. The Encoding was using “Latin-US DOS”. Why? How? I’m not sure, but that’s was what it was set to. When I finally discovered this preference, switching it to UTF-8 ended my Character Set Hell once and for all.

![Connection encoding properties dialog box in NavCat](/img/navconnectionprops.png)