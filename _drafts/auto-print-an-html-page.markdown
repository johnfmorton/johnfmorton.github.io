---
title: Auto Print an HTML Page
date: 2011-06-12 11:19:24
layout: post
url: http://supergeekery.com/geekblog/comments/auto_print_an_html_page
---
I recently worked on a project for a client who wanted to allow a user to view and print a number of recipes directly from a Flash banner. There were all the other ways of interacting with the recipes as well, like emailing, sharing on Facebook, and sharing on Twitter. (I don't go into those other thing in this post, but you can find me talking about how to do things like that elsewhere on SuperGeekery.)

Additionally, the client wanted these same recipes sharable and accessible from their web site. Those site-based recipes also needed to be printable.

I wanted a solution that didn't end up creating multiple versions of the recipes. There were already many moving pieces in the project and having multiple versions of those recipes floating simply increased the chances of errors creeping in.

### The Goal - one recipe to rule them all.

The solution was to build a single HTML page for each recipe that had a print button that would trigger the printing functionality. We also needed some mechanism to trigger printing on load when that URL was accessed from the Flash banner. (I also had an additional goal of keeping each recipe short enough to fit on a single printed page.)

First take a look at a sample recipe.

[http://supergeekery.com/auto-printable-html/sample_recipe.html](http://supergeekery.com/auto-printable-html/sample_recipe.html "Same page without autoprint turned on.")

You'll see a basic recipe with a print button that triggers the print function of your computer. Being able to print via a button click requires javascript, but the print button is hidden when there is no javascript on the page in the _noscript_ tags at the bottom of the page, so you'll only see it if you do have javascript available.

	<noscript><style type="text/css">#printbutton{display:none};</style></noscript>

Now take a look at the same URL that we used when calling it from a Flash banner. It's the same URL, but with GET variables added to the end:

[http://supergeekery.com/auto-printable-html/sample_recipe.html?print=true](http://supergeekery.com/auto-printable-html/sample_recipe.html?print=true "Same page without autoprint turned on.")

One you click on that link, it should launch the recipe like before but now your print dialog box should also appear.

### Step 1: Build the HTML & CSS

If you looked at the HTML for the sample recipe, you'll see it uses tables to keep the graphics all lined up. This was originally built to also be used as an HTML email. That's why it looks like HTML from 1997\. :-) That's just the state of making HTML emails. Typically you wouldn't use tables to align your graphics.

There were a number of recipes that were in this collection and some of them were long enough that they made the recipes spill over onto a 2nd page. We wanted to keep each recipe to a single page , so the big burger image needed to be replaced at the bottom of the page for printing.

The print style sheet basically turns off burger image at the bottom of the page with a solid rule, leaving the 'cleanbase' image, a solid rule, on. The web style sheet does the opposite.

From the Web style sheet:

	#cleanbase {
		display: none;
	}

From the Print style sheet:

	#fullburgerimage {
		display: none;
	}

The both style sheets also hide the "Print" button so that it's not visible unless told to be visible by the Javascript.

### Step 2: Use Javascript to make the print button work.

As I said before, none of what we're going over is new. It's just putting together several well-known techniques in a single page. It just works and that makes it handy to know.

The print function is simple javascript. You print the contents of the window element like this:

	window.print();

Now we just need to have that function triggered when a user clicks the print button.

	// Send coupon to printer 
	$('#printbutton a').click(function(event){
		event.preventDefault();
		window.print();
	});

### Step 3: Checking the GET variables and triggering the print function

In the jQuery document.ready function, I wanted to check for the variables passed into the page via the URL, basically, I wanted to check the GET variables. Doing this in PHP would have been simple, but I wanted to use Javascript to do this.

[Stack Overflow](http://stackoverflow.com/questions/5638760/javascript-invalid-quantifier-error-can-someone-help-me-see-my-mistake "Stack Overflow GET variables and JavaScript") to the rescue. Here's a great little function to do that very task.

	function $_GET(q,s) { 
		s = s ? s : window.location.search; 
		var re = new RegExp('&'+q+'(?:=([^&]*))?(?=&|$)','i'); 
		return (s=s.replace(/^\?/,'&').match(re)) ? (typeof s[1] == 'undefined' ? '' : decodeURIComponent(s[1])) : undefined; 
	}

Now you just need to test to see to see if 'print' is 'true'. If it is, we trigger the print window function when the document is ready.

	if ($_GET('print') == 'true') {
		window.print();
	}

### Step 4: Finishing up

There are a few additional things this sample doesn't show that you could do.

*   Add the Open Graph meta data for this page.
*   Add Facebook 'like' and 'sharing' buttons
*   Add a Twitter share button
*   Build a email this functionality to this page.
*   Add Google Analytics to the page to see how people are using it.

Please comment below if you've got any feedback. Thanks.