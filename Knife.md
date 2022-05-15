# Abstract
Welcome back! I've been freshly granted my Network+ (which makes me a Secure Infrastructure Specialist now, I guess?) and I'm feeling like putting some of that to work, so let's hack another machine! This time we'll be targeting Knife on hackthebox.eu. 

## Enumeration

Kicking things off with an nmap scan, we see there isn't a whole lot in the way of open ports:

> sudo nmap -sC -sV -O -oN initial_scan.txt 10.10.10.242

![image](https://user-images.githubusercontent.com/6416242/168475628-69576628-bbe5-438a-85a5-75b8c0c7367e.png)


> Open ports:
> 22 - ssh
> 80 - http

Since my last writeup I picked up a neat plugin called Wappalyzer that gives me a brief overview of what a web app is leveraging without having to fire up BurpSuite or delve into Developer Tools. Am I the laziest hacker in the world? Possibly. But do I get results? Also possibly. Wappalyzer gives us two useful nuggets of info here.

![image](https://user-images.githubusercontent.com/6416242/168475638-363df1ef-96bb-4bb4-a7a6-3f7031d2c315.png)


## Exploit

Both our apache and PHP versions are a little out of date. I went to exploit-db and ran searches for both of them. While a few different options appeared for a range of Apache versions, something very specific jumped out at me when I searched for php 8.1.0:

![image](https://user-images.githubusercontent.com/6416242/168475739-3934d1ff-1182-4730-8d9e-00faeecb08cd.png)


This version of php was released with a backdoor that seems pretty easy to exploit. Let's take a look at the linked blogpost:

> https://flast101.github.io/php-8.1.0-dev-backdoor-rce/

This article gives a better description of the exploit and links us to a reverse shell script we can copy into our directory. With this in hand, we should be able to get our foot in the door. Run the python script, provide the requested inputs, and we're in! We check the usual hiding place for user.txt and find the flag.

![image](https://user-images.githubusercontent.com/6416242/168475671-9c29b0a5-475d-4207-9a65-73a23eca339d.png)


## Escalation

Now that we're user, let's see what we can do. Running sudo -l shows us that James, our shiny new identity, can run /usr/bin/knife with elevated privileges. One quick search of GTFOBins, one of the most useful sites for privilege exploitation I've ever seen, gives us a one-liner to become root.

> sudo knife exec -E 'exec "/bin/sh"'

We cross our fingers, copy it into our command line because we're too lazy to type it, hit enter and we've got our root privs! From there it's just a matter of finding root.txt. Root.txt is exactly where you think it is

![image](https://user-images.githubusercontent.com/6416242/168475681-1a73944f-824a-439d-a390-70c7766682fa.png)


## Takeaway

When I first started poking at this box I went down the rabbit hole with the Apache version. While I'm sure there's an Apache exploit that could work for this machine, the easier answer was right there, just waiting to be found beyond the nmap results. The PHP version can be found with Wappalyzer, BurpSuite, even developer tools. The big lesson here? Look for what you know is there. If it's a website, what's the web server running? What's the site running? What happens when you load the page? The old adage that the three most important words in hacking are "enumeration, enumeration, and enumeration" still very much holds true. Oh, and don't be like me and deep-dive on the first piece of information you get. You'll wind up running weird metasploit modules at three in the morning that only break you into your own smart coffee pot. 

I hope you found this writeup useful, and as always, happy hacking!
