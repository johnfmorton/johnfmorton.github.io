---
title: Making MAMP Pro domain names resolve in VMWare Fusion
date: 2012-09-20 12:24:15
layout: post
url: http://supergeekery.com/geekblog/comments/making-mamp-pro-domain-names-resolve-in-vmware-fusion
---
I use [MAMP Pro](http://www.mamp.info/en/mamp-pro/ "MAMP Pro homepage") quite often to develop locally on my Mac. Getting **VMWare Fusion** to recognize my host names require jumping through a few hoops, but getting it working is key to getting my work done.

Here’s how I do it.

### Set up MAMP Pro

Under _Server_, I set up my MAMP server to run on the default ports: Apache 80 and MySQL: 3306\. Running Apache/MySQL as user can be set to either setting.

Under _Hosts_, I make my _Server Name_ be whatever I’m working on, like “**mytestsite**”, for example and the _Disk location_ will point to the directory that site lives in.

At this point, “**mytestsite**” should resolve in the local Mac browsers.

### Set up VMWare

Now let’s turn to VMWare Fusion. My VMWare network setting need to be set to _Bridged (Autodetect)_.

To be sure you can access the regular Internet from a VMWare browser, go look at some regular site on the Internet from your Windows installation. Good? Let’s move on.

### Setting up your Windows “hosts” file

Now for the trick… You need to edit your “hosts” file in your Windows installation in VMWare. This requires a bit of jumping through hoops.

In your start menu, look for NotePad. It’s under _All Programs > Accessories > NotePad_. **Don’t just click to open though**, use a right-click to open it and choose “Run as administrator”. You will have to agree via a dialog box.

Now that you have Notepad open, select “File > Open” dropdown menu and search for your hosts file. It’s most likely path is like this:

	C:\windows\system32\drivers\etc\hosts

You’ll need to add a new line to your hosts files for each of the “Server Names” you have in MAMP Pro. Each line will be your Mac’s local network address and the “Server Name”.

For example, my local IP address for my Mac is 192.169.11.10\. (I found this out in my System Preferences > Network preference pane.) My “Server Name” is “mytestsite”. This means I would add the following to the bottom of the hosts file in my Windows environment:

	192.168.11.10 mytestsite

Since you’re running Notepad as an administrator, you should be able to save your file and immediately see the results. Open Internet Explorer and visit “mytestsite” and you should see the site served from your MAMP Pro on your Mac.

Joy! Now you get to debug your site for IE.