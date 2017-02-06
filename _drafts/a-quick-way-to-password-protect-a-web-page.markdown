---
title: A Quick Way To Password Protect a Web Page.
date: 2010-02-26 06:22:46
layout: post
url: http://supergeekery.com/geekblog/comments/a_quick_way_to_password_protect_a_web_page
featuredImage: passwords_scottie.jpg
---
There are a variety of ways to password protect pages online. Perhaps the easiest is to use an _.htaccess_ file. The reason this is the easiest way is because it’s built into the control panel at many hosting companies. I use [Dreamhost](http://www.dreamhost.com/r.cgi?86973) and it’s control panel makes it automatic. Sweet. If you don’t have that as part of your hosting package, check out [this article at htmllite](http://www.htmlite.com/HTA006.php) for a walk through on do it yourself.

### What I don’t like about the .htaccess method

![Example of what htaccess password protection looks like.](/img/htaccess_example.png)

The _.htaccess_ method is very secure and time-tested. Plus, if your host supports it, it’s really easy to set up. The part I don’t like though is the style of log in window. Depending on your browser and operating system, you will see the log in window in a variety of ways. All of them feel like ‘system’ level boxes, usually gray boxes that you can’t customize.

There’s also no easy way to logout once you’re logged into a session without shutting your browser entirely.

### There is another way.

I’m going to show you a technique that is pretty easy to set up. You can probably even [download](http://assets.supergeekery.com/sg_simple_password_protection.zip "Download the sample files.") the example files then simply place a single line at the top of any page you want protected and call it a day. The login page is also just HTML, so you can style it any way you want.

You’ll notice all the files have the extension of _.php_, not _.html_. It also assumes your server is PHP based. PHP is doing the checking and validation, so you need to have .php at the end of your files to get them processed as PHP. That means these files won’t work if you’re just viewing them from your desktop. They need the webserver to process them. If you want to know more about doing this on your desktop a WAMP or MAMP is worth looking into. ([Learn about WAMP and MAMP on Wikipedia.](http://en.wikipedia.org/wiki/WAMP)). I use MAMP almost everyday. You can [get a free version](http://www.mamp.info/en/index.html) from their site. It’s easy to set up and use. You can also just upload them to your server and do your testing there. It should work fine.

This isn’t a new technique, but since I’ve been asked how to do this a number of times, I thought I’d share this technique here on SuperGeekery.

### The 5 files

If you haven’t already, [download](http://assets.supergeekery.com/sg_simple_password_protection.zip "Download the sample files.") the .zip archive of the files, but you don’t need to, I’ll go over them below. There are 8 files in the archive, but you only need 5 of them. The other 3 files are example on how to use them to protect and style the pages.

1.  **login.php** - The page that asks for your password.
2.  **checkpw.php** - This checks to see if the password entered by the user is the same as what you’ve specified.
3.  **wrong.php** - What else are you going to call a file that when it tells someone they are _wrong_.
4.  **logout.php** - You’ll never see this file on screen. It just logs a user out.
5.  **protect_page.php** - This is the magic file. It’s what protects any page you choose.

### login.php

The login file below is the basic login page. I’ve got some divs and spans in it that I use to style the page. You can get rid of those if you don’t want them. In the download, you can see the style I’ve applied to the page with the file called _pwstyle.css_. Here’s the login.php file.

	<!DOCTYPE HTML>
	<html lang="en-US">
	<head>
	    <meta charset="UTF-8">
	    <title>Log in</title>
	    <link rel="stylesheet" media="all" href="pwstyle.css" />
	</head>
	<body>
	    <form name="form1" method="post" action="checkpw.php">
	        <div id="container">
	            <p>
	            <span id="pass">Password:</span>
	            <span id="formfield"><input name="pw" type="text" id="pw"></span>
	            <span class="submitbt"><input type="submit" name="submitbt"></span>
	            </p>
	        </div>
	    </form>
	</body>
	</html>

You can see it’s just a form with a submit button. The action is to go to “checkpw.php” to check the input.

### checkpw.php

This file takes the text that the user entered in the login form. The text is stored in a variable called $_POST['pw'] and it is compared to the password which is **hardcoded** in the page. You can see the password is “swordfish” in the code on line 10.

This is a simple password protection scheme. It’s checking again a single password. You can make it more robust by having a database plus checking against a combination of a name and password, but that’s more complicated than I’m showing here. I used a stripslashes command here in case you end up using it with a database. ([Click here for more info on stripslashes.](http://php.net/manual/en/function.stripslashes.php "Read the PHP manual on stripslashes"))

	<?php
	// pw is the password sent from the form 
	$pw=$_POST['pw'];

	// the stripslashes is included in case you end up sending this to a database. It's to help prevent hackers compromising your system.
	$pw = stripslashes($pw);

	// you can make this much more robust by checking against a database in this file at this point

	if($pw == 'swordfish'){
		session_register("pw"); 
		header("location:index.php");
	} else {
		header("location:wrong.php");
	}
	?>

The last bit above is checking whether the user’s password and the required password are the same. When the user gets the password right, we register a session cookie called _pw_ use a header redirect to send the user to the content, index.php. If their password doesn’t match our password, we send them to wrong.php.

### index.php

I don’t have any code to share with you yet for the main page, I’ll cover how that page is protected near the end of this post. I’ll show you how to protect any page you want at the same time.

### wrong.php

This is basically just a page that says, “your password is wrong” and has a link back to the login page.

	<!DOCTYPE HTML>
    <html lang="en-US">
    <head>
        <meta charset="UTF-8">
        <title>Log in</title>
        <link rel="stylesheet" media="all" href="pwstyle.css" />
    </head>
    <body>
            <div id="container">
                <p>The password was incorrect. <a href="login.php" title="try again">Try again.</a></p>
            </div>
    </body>
    </html>

### logout.php

Another simple page. All it’s basically doing is destroying the session cookie, then send them back to the login page, but you could send them anywhere you wanted to.

	<? 
	session_start();
	session_destroy();
	header("location:login.php");
	?>  

### protect_page.php - the magic page

	<? 
	session_start();
	if(!session_is_registered(pw)){
	header("location:login.php");
	}
	?>

I think of this as “the magic page” because making this code it’s own file makes it easy to quickly unprotect files. You could simply put this code at the top of any page you wanted to, but when you embed it, you can simply comment out the content within one file and make every file you had protected no longer protected all at once. You comment it out as follows:

	<? 
	/* session_start();
	if(!session_is_registered(pw)){
	header("location:login.php");
	}*/
	?>

### Protecting your index.php

You may be asking yourself why the login.php file wasn’t named index.php since the login page is what people see first anyway. I do it this way because the real content of your site or directory isn’t the login page, it’s the actual content, and you may want to unprotect your files at some point. If your homepage was at protectedpage.php instead of index.php, you’ll either have to tell people to visit http://mysite.com/protectedpage.php, write a new htaccess file to make that the default page for that directory, or change file names and links inside a bunch of pages to make the non-protected scheme work.

Below you’ll see a sample index.php page that has my ‘real’ content in it. It’s protected by starting the page with a simple line: <? include('protect_page.php')?>. It’s important to note that this path assumes everything is in the same directory. If you’ve got subdirectories, you may want to adjust the path to the protect_page.php to something like <? include('/protect_page.php')?>. You may also need to adjust paths in the rest of the files to something similar.

	<? include('protect_page.php')?>
	<!DOCTYPE HTML>
	<html lang="en-US">
	<link rel="stylesheet" media="all" href="pwstyle.css" />
	<head>
	    <meta charset="UTF-8">
	    <title>This is password protected</title>
	</head>
	<body>
	    <h1>You can only see this with a valid password.</h1>
	    <p>There is a <a href="page2.php" title="Another password protected page.">another password protected page.</a> Try visiting page2.php when you have logged out and you will end up at the log in screen again.</p>
	    <h2>How do you use this?</h2>
	    <ol>
	        <li>You can password protect any page by including the include at the top of this index.php page and any other page you want protected by the password.</li>
	 
	        <pre>&lt;? include('protect_page.php')?&gt;</pre>
	        <li><p>The pages must have a .php, not a .html. PHP is what is checking for the credentials before displaying the page.</p></li>
	        <li>To allow a user to log out, include a link to the logout page.</li>
	    </ol> 
	 
	    <p><a href="logout.php" title="logout">Click here to log out.</a></p>
	</body>
	</html>

### Protecting other pages.

As I mentioned, protecting any page is as simple as including <? include('protect_page.php')?> at the top, but an example always helps me out, so you can see below how I’ve done that.

	<? include('protect_page.php')?>
	<!DOCTYPE HTML>
	<html lang="en-US">
	<link rel="stylesheet" media="all" href="pwstyle.css" />
	<head>
	    <meta charset="UTF-8">
	    <title>A second page</title>
	</head>
	<body>
	    <h1>A Second Protected page</h1>
	    <p>This page is also password protected by the include statement at the top of this document.</p>
	    <pre>
	&lt;? include('protect_page.php')?&gt;
	    </pre>
	    <p><a href="logout.php" title="logout">Click here to log out.</a></p>
	</body>
	</html>

That’s it! Go forth and protect.