# Abstract
Welcome back! I've been freshly granted my Network+ (which makes me a Secure Infrastructure Specialist now, I guess?) and I'm feeling like putting some of that to work, so let's hack another machine! This time we'll be targeting Knife on hackthebox.eu. 

## Enumeration

Kicking things off with an nmap scan, we see there isn't a whole lot in the way of open ports:

> sudo nmap -sC -sV -O -oN initial_scan.txt 10.10.10.242



> Open ports:
> 22 - ssh
> 80 - http

Since my last writeup I picked up a neat Firefox plugin called Wappalyzer that gives me a brief overview of what a web app is leveraging without having to fire up BurpSuite or delve into Developer Tools. Am I the laziest hacker in the world? Possibly. But do I get results? Also possibly. Wappalyzer gives us two useful nuggets of info here.



## Exploit

Both our apache and PHP versions are a little out of date. I went to exploit-db and ran searches for both of them. While a few different options appeared for a range of Apache versions, something very specific jumped out at me when I searched for php 8.1.0:



This version of php was released with a backdoor that seems pretty easy to exploit. Let's take a look at the linked blogpost:

> https://flast101.github.io/php-8.1.0-dev-backdoor-rce/

This article gives a better description of the exploit and links us to a reverse shell script we can copy into our directory. With this in hand, we should be able to get our foot in the door. Run the python script, provide the requested inputs, and we're in! We check the usual hiding place for user.txt and find the flag.


## Escalation

Now that we're user, let's see what we can do. Running sudo -l shows us that James, our shiny new identity, can run /usr/bin/knife with elevated privileges. One quick search of GTFOBins, one of the most useful sites for privilege exploitation I've ever seen, gives us a one-liner to become root.

> sudo knife exec -E 'exec "/bin/sh"'

We cross our fingers, copy it into our command line because we're too lazy to type it, hit enter and we've got our root privs! From there it's just a matter of finding root.txt. Root.txt is exactly where you think it is


## Takeaway

When I first started poking at this box I went down the rabbit hole of trying to find a vulnerability with the Apache version. While I'm sure there's an exploit that could work for this machine, the easier answer was right there, just waiting to be found beyond the nmap results. The PHP version can be found with Wappalyzer, BurpSuite, even developer tools. The big lesson here? Look for what you know is there. If it's a website, what's the web server running? What's the site running? What happens when you load the page? The old adage that the three most important words in hacking are "enumeration, enumeration, and enumeration" still very much holds true. Oh, and don't be like me and deep-dive on the first piece of information you get. You'll wind up running weird metasploit modules at three in the morning that wind up breaking you into your own smart coffee pot. 

I hope you found this writeup useful, and as always, happy hacking!
