---
layout: post
title: OWA Honeypot + pastebin.com = 60 seconds
author: @_w_m__
---

If you've ever done an external pentest or redteam exercise then you will know that OWA (Outlook Web App) is your friend. Everything from bruteforcing, dropping mail rules ala [ruler](https://github.com/sensepost/ruler) or just scanning mailboxes for useful info such as passwords etc.

Chances are that you got in using some form of a password attack, bruteforce, spraying or what ever floated you boat on the day. And you most likely noted in your report that a weak password policy, user password reuse and lack of MFA (Multi Factor Authentication) is to blame. And that is really sound advice, but more often than not companies for one reason or another tend to ignore these recomendations. Maybe strong passwords/phrases are to hard to remember, maybe MFA gets in the way of users. 

The other very common thread is that companies/organisations often strugle to understand why someone would go after them. They don't hold credit card data so why? 

But I'm not going down that rabbit hole. What I was curious about was how quickly, if at all, would an OWA stack become a target if possible credentials become "public". I'm not talking about your usual internet scanners, shodan etc. 

## ./configure
To start I would need an OWA instance, which 1. I didn't have and 2. being lazy I wasn't going to setup. Just way to much effort, so after googling for a bit and not finding a OWA based honeypot that I liked so I quickly slapped one together using python flask and some pretty hacky code, [https://github.com/joda32/owa-honeypot](https://github.com/joda32/owa-honeypot). 

![OWALandingPage](https://raw.githubusercontent.com/joda32/owa-honeypot/master/docs/OWA_honeypot_1.png)

I added some touches like the favicon, fonts and tweaking the HTTP Response headers to at least look like a Windows/OWA box. I also added some routes that you would normally find with OWA (EWS etc) and put some basic auth on them. Lastly, I decided to copy include some IIS/.Net 404, 403 and 401 pages again just to make it less obvious that this is a honeypot.

![OWA404](https://raw.githubusercontent.com/joda32/owa-honeypot/master/docs/OWA_honeypot_4.png)

I registered a super cheap domain from NameCheap and got the honeypot running on a $5 DigitalOcean VPS.

## ./make
Now if I just left it like that, chances are that the only traffic I would see in the logs is generic internet noise, scanners, search engines, bots etc and that is exactly the traffic that I'm not interested in. I needed to get some fake details of this honeypot out somewhere and see if someone would notice. Pastebin sounded like a good idea, I mean if you wanted to put some crap on the internet, that is probably the place to put it. 

To create the post I simply searched for a paste with the word "Hacked" in it, copied it, cleaned up the data a bit and replaced the domains of the email addresses in the orignal post with the domain that I purchased. Also I changed the link of the "supposed" breached site. And then I just posted it back onto pastebin.

## 60 seconds
To be honest I didn't expect much, I would have settled for someone browsing to the honeypot. 60 seconds was all that it took for the first login attempt using "creds" from the fake breach. Over the next 45 min another 5 different IPs tried various "creds" all from the fake breach. Also, based on the HTTP logs, this looked like actual browsers, not just based on the User-Agent, but also the way and the sequence that resources were requests from the honeypot

![HTTPLog1](https://raw.githubusercontent.com/joda32/owa-honeypot/master/docs/OWA_honeypot_2.png)

Also not one "attacker" used the full list. The most one attacker tried was 5 sets of creds, randomly picked from the breach. 

![HTTPLog2](https://raw.githubusercontent.com/joda32/owa-honeypot/master/docs/OWA_honeypot_3.png)