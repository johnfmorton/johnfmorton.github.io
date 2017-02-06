---
title: 'Craft CMS: A bigger “Plain Text” field.'
layout: post
date: 2016-12-21 10:22:17
---
I inherited a Craft CMS site that I now maintain for a new client. When you inherit other people's code, you need to see the project from their POV to get an understanding of they see things working. Luckily Craft seems to encourage logical structures being built so I've not had too much trouble getting up to speed on this site.

One of the problems I did run into was with a simple Plain Text field. This client authors HTML emails in some program that generates HTML code that then gets pasted into a single Plain Text field in Craft. This process has worked for them so I hadn't investigated that part of the site much yet. 

On their latest newsletter though, the text in the Plain Text field was getting truncated after they tried to save the entry. 

It turned out to be the MySQL database itself doing to truncating of the HTML code. If you don't specify a maximum length to a plain text field when you create it the Craft control panel will not limit the user from entering any amount of text but the database will do it anyway.

What happens is that the column that gets created in the MySQL database is a "text" field which will limit the content to 64K, roughly 64,000 characters. Any content that is longer than that just gets dropped off the end. That's what was happening to my client.

To remedy this, just go into the field creation page in the Craft control panel and give the text field a maximum length greater than the 64,000 limit. I put in 90,000 characters and Craft updated the table to a "mediumtext" column which can store 14 megabytes of text which is plenty of room to hold the long HTML content the client was creating. 