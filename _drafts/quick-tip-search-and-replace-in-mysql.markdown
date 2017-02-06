---
title: "Quick tip: Search and Replace in MySQL"
date: 2010-03-10 06:16:51
layout: post
url: http://supergeekery.com/geekblog/comments/quick_tip_search_and_replace_in_mysql
---
During the rebuild of this site, I ended up doing a [Movable Type Export](http://supergeekery.com/geekblog/comments/quick_tip_expression_engine_movable_type_export) from my old database and an reimportation into a new "clean" database. It worked decently, but I've still got cleaning up to do in some older posts. Doing a _search and replace_ within my database seemed like the best way to fix the biggest offenders in my posts and that's how I cleaned up the most offending cruft that crept in.

Before you try any of this on your own database, **make a back up of your database**! This proceedure isn't something you can undo easily if you mess up and a single character mistake can mess it all up. I use [NavCat for MySQL](http://navicat.com/en/products/navicat_mysql/mysql_overview.html) and it has a handy feature that makes a back up of your database and it lets you quickly restore it from a back up with a click. It's $80 which is not cheap, but how easily it makes backups and restores has made it worth it for me. I did a backup for every 2 or 3 of these 'search and replace' commands that I'm going to show you. I did end up needed to restore once as well. "[Measure once, cut twice](http://en.wiktionary.org/wiki/measure_twice_and_cut_once)," definitely applies to what we're doing here.

### Get to know your Expression Engine 2 databases

The first thing you need to know is where the data is located that you want to change. You might need a road map to the EE2 databases. [Check out this overview of EE2 data that Leevi Graham has posted](http://emberapp.com/leevigraham/images/expressionengine2-table-schema/sizes/o). It's a good place to start if you're doing this in EE2\. (If you're doing this in something other than EE2, everything should still work the same. You'll just have to figure out the proper tables and fields on your own.)

The reason that this is important is that you need to know where the data is stored that you want to manipulate. In my case, the table name was _exp_channel_data_ and the row I wanted was _field_id_2_ since I wanted to alter the body area of my posts. Yours may be different though. You **must** check your database before you try this. Below is a screen shot from phpmyadmin that shows you what I checked.

![Check phpmyadmin data to determine the right field to alter.](/img/sg_myphp_searchreplace_620.png)

On the left side of this screen shot, you can see I've got the _exp_channel_data_ table selected. I've then clicked the _Browse_ tab, so I can look at the data stored in that table. In _field_id_2_ I can see the beginning of multiple article's body area elements. This is the data I want to alter.

### How to replace "localhost" and replace it with "supergeekery.com"

Now that we know those vital pieces of info, we need to update that table. If you're in phpmyadmin, you need to click the _SQL_ tab at the top.

![Search and Replace](/img/sg_myphp_searchreplace_2_620.png)

What does the SQL command do? We're going to reset the data that's field_id_2 with the result of a search and replace command where we're asking it to take every instance of _http://localhost/_ and replace it with _http:://supergeekery/_. Here's how we say that.

	UPDATE `exp_channel_data` SET exp_channel_data.field_id_2 = replace(exp_channel_data.field_id_2, 'http://localhost/', 'http://supergeekery/');

Now go back and check to be sure you've got the expected results. You can click back in the Browse tab and check your data to be sure what you wanted to happen did happen, if not, reimport your backed up data and start again. Good luck!