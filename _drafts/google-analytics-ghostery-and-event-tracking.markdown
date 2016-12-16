---
title: Google Analytics, Ghostery, and Event Tracking
date: 2013-06-01 16:18:00
layout: post
url: http://supergeekery.com/geekblog/comments/google-analytics-ghostery-and-event-tracking
---
At the time I write this post, Google Analytics has a new version in beta called “Universal Analytics”. It’s basically Google Analytics version 3\. This means that code you may have relied on during the version 2 of Google analytics may no longer work.

Google suggests that all new projects moved to version 3 now. It does offer “real time” analytics, so you can see who is on your site at that very moment. That’s pretty cool.

If you want just the basics, it is extremely easy to set up. Google provides simple asynchronous JavaScript that you can place in the head of your HTML document to get the analytics up and running quickly. You can get started on [the Google Analytics Overview page](https://developers.google.com/analytics/devguides/collection/analyticsjs/ "Google Analytics Overview").

### Event Tracking

I’ve been setting up a site where the basic configuration did not collect all of the data that my client wanted to collect. The basic implementation of Google Analytics will track page views. But I wanted to track clicks on certain buttons on the site which is beyond page tracking.

That meant I needed to rely on event tracking in addition to the page view tracking. What are “events” when you refer to analytics? According the documentation, “event tracking allows you to measure how users interact with the content of your website. For example, you might want to measure how many times a button was pressed, or how many times a particular item was used in a web game.”

My site was already using jQuery, so it seems like I could rely on the suggested code structure provided in the [Google Analytics Event Tracking documentation](https://developers.google.com/analytics/devguides/collection/analyticsjs/events "Google Analytics Event Tracking documentation") .

<pre class="brush: js">// Using jQuery Event API v1.3
$('#button').on('click', function() {
  ga('send', 'event', 'button', 'click', 'nav-buttons');
});</pre>

This code **does** work in many circumstances, but I wanted to track buttons that lead to other pages on the website. The problem was that if I did that using this sample code my events were not recorded because the action that the user was taking, the clicks away from the page to a new page, prevented the analytics from being collected due to the page being refreshed. I needed to delay the actual page request until I was sure the event had been recorded. I need a callback. Take a look at the [hitCallback entry in the Field References area of the documentation](https://developers.google.com/analytics/devguides/collection/analyticsjs/field-reference#hitCallback "hitCallback documentation").

This lead me to have event tracking that looked something like this:

	$('#module-coupon').on('click', function(event) {
	  event.preventDefault();
	  ga('send',  {
	    'hitType': 'event',
	    'eventCategory': 'module-area',
	    'eventAction': 'click',
	    'eventLabel' : 'coupon-button',
	    'hitCallback' : function() {
	      document.location.href = event.currentTarget.href;
	    }
	  });
	});

This is the verbose version of the code, but it helps you see what all the values mean. Let’s go through it line by line. Imagine this is for a web site that has a series of buttons in a module area on the page. Each of those buttons links to a deeper page in the site.

The function opens by adding a listener for a click on an a tag with the ID of “module-coupon”. A click will trigger an anonymous function, which we’re passing the event into using “event”.

Next it prevents the default action, which would be to take a user away from the page, with the event.preventDefault() command. The word “event” here is the “event” we passed in with the anonymous function.

Now we begin the Google Analytics code with the “ga” function by passing in “send” along with an object of all the data it needs. (You can see the “ga” function set up your analytics by passing in “create” and then “send” in the default quick start code Google provided.)

The ‘hitType’ is an ‘event’ since we’re tracking events. (The docs (https://developers.google.com/analytics/devguides/collection/analyticsjs/field-reference#hitType) say it must be one of ‘pageview’, ‘appview’, ‘event’, ‘transaction’, ‘item’, ‘social’, ‘exception’, ‘timing’.)

The ‘eventCategory’ is up to you. You can put anything you want in here, but the category will help you narrow your focus in your actual analytics control panel. In my case, I called this “module-area” since it’s an event (a click) in the module area of the page.

The ‘eventAction’ is also up to you. I used the word ‘click’ because it described the actual action, but you can choose whatever text fits your need.

The ‘eventLabel’ is also up to you. You can see I’m using it to get very specific with the name of the button that was clicked.

The ‘hitCallback’ is the key though. Since we prevented the default behavior of the user’s action, a clicking through to a new URL, we still need to execute that click through once we’ve got confirmation from Google Analytics that it has recorded the event. The hitCallback’s anonymous function will be executed when that confirmation arrives. We want to make the web browser go to the href of the link that was clicked.

That’s it. It all looks like it should work.

And it does.

Mostly.

### Ghostery and other privacy add ons.

I say it works mostly because it does. This should work, but what if Google Analytics didn’t get to load on the page? You’ve prevented the user’s default behavior from happening with the event.preventDefault() function and you’re counting on the Google Analytics hitCallBack to complete the user’s intended action. If Google Analytics was prevented from loading, by, for example, using [Ghostery](http://www.ghostery.com/ "Ghostery homepage"), the privacy add on for Chrome, Opera, Firefox, Safari and IE.

If a user has Ghostery, you’ve now prevented them from being able to navigate your site with the buttons you’re tracking!

Initally I thought I needed to just check for the presence of the “ga” function. This didn’t work though. Ghostery allowed the Google Analytics to create the ga object so simply testing for the presence of it wasn’t enough. I need to test for the presence of the ‘create’ function inside the ga object. If the ga.create function wasn’t present, that means Google Analytics instance couldn’t be created. So what does that leave the event tracking looking like? I’m glad you asked.

	$('#module-coupon').on('click', function(event) {
	 	if (ga.create)
	      event.preventDefault();
	      ga('send',  {
	        'hitType': 'event',
	        'eventCategory': 'module-area',
	        'eventAction': 'click',
	        'eventLabel' : 'coupon-button',
	        'hitCallback' : function() {
	          document.location.href = event.currentTarget.href;
	        }
	      });
	    }
	});

This updated function basically wraps the event tracking, including the preventDefault portion, in a test for the page’s ability to create a Google Analytics instance. If the page can’t create it, then just let the user click through like they intended.

### Before I close… Any advice?

I wanted to include a quick aside about the “Category” “Action” and “Label” values I used above. I’m not sure I’ve got the best method for how I think of these different variables. Google’s documentation seems to suggest the way I think of event categories and event labels are backwards. If you have thoughts on how to use these, please post a comment. Thanks.