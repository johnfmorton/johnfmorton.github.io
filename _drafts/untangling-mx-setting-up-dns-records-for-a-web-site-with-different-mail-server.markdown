---
title: "Untangling MX: Setting up DNS records for a web site with different mail server."
date: 2011-04-06 13:32:14
layout: post
url: http://supergeekery.com/geekblog/comments/untanglin_mx_setting_up_dns_records_for_your_web_site
featuredImage: dns_tangle_640x200.png
---
I recently had to set up an MX record for a client and although I’ve set them up before, it suddenly made sense to me in a way that it hadn’t before. Perhaps my story will help someone else have a similar epiphany.

### Basic name server set up.

My client had their site and their mail all handled by the same serving company. This meant their name server records were basic. There were 3 entries and that was it. All traffic to their domain used the same records. It could be web traffic or mail traffic. It didn’t matter. They were something like this. (I’m using ‘dummy’ URL names in this post, like mynameserver.com and my.domain.com. Don’t take those literally. Use your own info where appropriate.)

	ns1.mynameserver.com
	ns2.mynameserver.com
	ns3.mynameserver.com

### The problem.

My client came to me because their site was painfully slow. Their hosting plan was a shared hosting plan and their site traffic demanded had outgrown what the server was able to handle. Their site needed to be on a dedicate, robust server. I did some research and presented them with a recommendation, got their sign off, and moved the site successfully. (The site was an Expression Engine site. I moved it to Engine Hosting. That’s not really that important to what I’m going over here though.)

The tricky part of the move was that they were not ready to move their mail to a new server. It need to stay where it was. That meant going from an _“easy”_ DNS set up, to a more complex set up. I had to split the MX (Mail Exchange) records off from the regular DNS records.

You might wonder why there was no MX record to begin with and how the email still managed to get to its destination in the old setup. Although I knew it worked, I never bothered to ask why. I found [an article on MX records on Wikipedia](http://en.wikipedia.org/wiki/MX_record "Wikipedia article on MX records") that had the answer.

> “If no MX records were present, the server falls back to A, that is to say, it makes a request for the A record of the same domain.”

If I only set up the DNS the easy way, an email message falling back to the A records would mean that message would be delivered to the new server where the web site was hosted, and not the old server where the mail was supposed to be handled. I needed to split the traffic by setting up an advanced DNS record.

### Advanced DNS record setting.

The domain name was registered with Network Solutions. The ability to set the MX record separately from the other records in the Network Solution interface is called _Advanced DNS Settings_. Other registrars allow you to do this as well though.

The _Advanced DNS Settings_ choice means Network Solutions will use their name servers, for example, **NS43.WORLDNIC.COM**. You need to manually set up the traffic behind that name server.

### The A records

_I don’t want to spoil the **surprise ending** of this post, but here’s a screenshot of where I needed to get to. It might help as I go through the nitty gritty._

![Proper DNS set up for separate MX and A records.](http://supergeekery.com/images/uploads/dns_mx_record_networksolutions_620x558.png)

I had to dig into the meat of the DNS records a bit more and set the **A record** (the main DNS record) and the MX record manually. For the A record I needed the actual IP of the server the site would eventually live on.

You can find this out the IP address of the A record with a ‘dig’ command from the Terminal on a Mac. (Windows folks, I’m not sure how to execute these commands on your platform. I’m sure there is a way though.)

Below is the command I would put into my Terminal. I’m executing a ‘dig’ asking for the ‘A’ record from my site’s name server, ns1.dreamhost.com, and specifying the domain I’m asking about, supergeekery.com. I also specify I just want the ‘short’ answer. Don’t overlook the ‘at’ symbol, the @, before the name server.

	dig A @ns1.dreamhost.com supergeekery.com +short

The result of this command is my IP address for SuperGeekery.com. That’s what needs to go into the A record, in essence what the name server translates the word ‘supergeekery.com’ into for computers to locate the server with it’s request for information.

_NOTE: I had already set up the new server to expect the traffic. Although I didn’t test this dig command before I set up the server, I wouldn’t expect the dig command to work until that is done. Want to know more about the ‘dig’ command? Check out this great article on dig. [http://www.madboa.com/geek/dig/](http://www.madboa.com/geek/dig/ "Everything you wanted to know about the dig command.")_

I used that IP address for the ‘www’ record, the ‘@’ and the ‘*’. That let a URL staring with ‘www’ point to the right domain, a URL that didn’t start with anything (ie, no ‘www’ in front of the domain name), and a ‘*’, to let anything unspecified to go to the IP address, ie mysubdomain.mydomain.com.

### One more A record

So the A records are set up. How do you set up the MX record? I incorrectly assumed I could use the IP address of the original server for this. The MX record needed to be a CNAME, a canonical name. A numeric IP won’t work here. So what CNAME should you place in the MX record field? It has be be created manually.

From everything I’ve read, there isn’t a rule to follow as what you choose for the name, but it does seem to be common to use ‘mail’ as the subdomain you’ll set up for this. This means you’ll be creating ‘mail.mydomain.com’ as an A record before you use it as your MX record.

First, create another A record, just like creating the ‘www’, ‘@’, and ‘*’ records. This time use ‘mail’ instead, which creates the A record for ‘mail.mydomain.com’. In the place where the numeric IP address is asked for, this time use the server’s IP address where the mail is processed. In my case, this was the IP address of the old server for mydomain.com.

### Finally, the MX record gets made.

Don’t stop here though. The MX record isn’t created yet. If you’re in Network Solutions, you have a button called “Edit MX Records”. Other registrars will have something similar. The MX record needs to be the same as the A record that was just created that points to the IP address for the mail server. In this case, it’s ‘mail.mydomain.com’, so put that into your MX record. (Take a look at the screenshot from Network Solutions I’ve added to this blog post. Notice ‘mail.mydomain.com’ appears twice in that screenshot.)

Now email to the mydomain.com server will check the MX record and see the address mail.mydomain.com which is pointing to the correct IP for your server, which is different from the web host. Whew!

Got some thoughts? Share them with a comment below.