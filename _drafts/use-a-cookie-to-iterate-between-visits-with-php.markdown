---
title: Use a cookie to iterate between visits with PHP.
date: 2013-04-10 12:01:03
layout: post
url: http://supergeekery.com/geekblog/comments/use-a-cookie-to-iterate-between-visits-with-php
---
Here's a small code snippet that I use to increment a value between visits using a PHP cookie.

I use it, for example, when I have 3 different images that could go on a page, and I want to serve up the next one in the sequence on subsequent visits to the page. I just use the "$num" where appropriate.

You can edit the '2' in line 14 to change the wrap around value to suit your needs. Happy PHPing.

	<?php
	///////////////////////////////////////////////////////////
	//- Purpose: increment from 0 - 2, then loop back around //
	///////////////////////////////////////////////////////////

	// Is a cookie set already?
	if ($_COOKIE['numcycle']!='') {
		// If cookie was corrupted, it will return 0
		// when evaluated as in int.
		$num = (int)$_COOKIE['numcycle'];
		$num++;
		// if the newly iterated $num is over our limit,
		// in this case 2, then reset it to 0
		$num = $num <= 2 ? $num : 0;
		// now reset the cookie with the new number
		setcookie('numcycle',$num,time() + (86400 * 30)); // 86400 = 1 day
	} else {
		// Cookie wasn't set, so let's set one.
		setcookie('numcycle','0',time() + (86400 * 30)); // 86400 = 1 day
		$num = 0;
	}

	echo $num;
	?>