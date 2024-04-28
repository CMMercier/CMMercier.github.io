---
title: "One Month Bug Bounty Journey Update"
date: 2022-03-24
description: "One Month Bug Bounty Journey Update"
menu:
  sidebar:
    name: One Month Bug Bounty Journey Update
    identifier: One Month Bug Bounty Journey Update
    weight: 8
---

## Introduction

My goal with this is to explain some of my thoughts and how they changed as I progressed and how I modified my path along the way. For we will always be learning in the field of Cybersecurity and there is no race to a finish line just finding the most time efficient path to learn as much as we can as we go. Lastly I want to mention when you feel like you are struggling and seeing everyone’s accomplishments on social media is getting you down, remember that they too struggled. Remember, Many of life’s failures are people who did not realize how close they were to success when they gave up.

## Initial Steps

When I started out I had run across a few web application testing checklists and thought that was a great way to start. The one I found to be most helpful is by six2dez and is located at https://github.com/six2dez/pentest-book/blob/master/others/web-checklist.md but it is also a part of their Pentest Book of resources at https://pentestbook.six2dez.com/. So far this is the only check list that I have run across that goes as far as listing the tools used and even linking to parts of their book that give usage examples so huge thanks to six2dez!

Initially I had decided that I was going to pick one program and run through the checklist the best I could while testing the tools and methods they used as well as others I could find to get a feel of what worked best for me. Also a lot of the tools and techniques that you run across in past resources will be outdated but are still a good guideline. I worked through the recon scopes of the list first, it’s very important to always begin on your target with a solid recon plan. But at the same time when I was going through initial recon on my single target I was testing out vulnerability scanners on it and other targets.

## Scanner Approach

On the side I have been running tools for a few reasons. I wished to discover which ones worked best and also to get some experience finding vulnerabilities in the wild. Investigating templates and doing further research online whenever there was a discovery was the most fun learning experience to me. Although everything that I have found this way has either been a false positive, already reported, or a disclosure program that does not reward cash. But there are a few real values in this approach still.

Firstly, I learned to tell what was more likely to be a false positive or not and how to research into many new and varying vulnerability types. Also that some things things that appear to be false positives or not serious enough to report can be chained or otherwise lead to a more serious vulnerability with some time and effort.

Secondly, this is free penetration testing experience. Whether its on paid or disclosure programs it is still real world experience and learning how to submit good reports even if its on disclosure only programs or only leads to duplicates will help you submit great reports to paying ones in the future that might even land you more of a reward if you impress them enough!

## Manual Approach

My approach has changed quite a bit since I first started. Currently what I am trying out Portswigger Academy, https://portswigger.net/web-security/all-materials. This is a free learning resource that is meant to be used alongside their Burp Suite Application. It is really meant to be used alongside the paid version but whenever labs do require it they tend to give alternative methods of completion. I picked a couple programs and as I learn sections on portswigger I then go and test them out on those programs. I do suggest having more than one program to test on as this helps to see differences in responses and how the developers think.

I know a lot of bug bounty hunters suggest picking a program that is wide scope and you can hunt on it longer. While there is indeed many benefits to this I feel like this approach is likely to be less successful to new hunters than in their times where there was much less competition. But I also believe having two to three programs to focus on is the best approach and that picking one with a large scope is in fact a good idea still. As for the other one or two program choices? Pick stuff you either use often or have an interest in using or learning. It’s a much more competitive field now and it’s easy to feel burned out not finding anything for long periods so have at least one program that helps keep that interest flowing!

## Conclusion

Manually picking a target or targets to test on for an extended period of time and diving in to your favorite learning resource (portswigger, pentesterlab, bugbountyhunter, etc.) will be the best path to finding undiscovered high impact vulnerabilities. If you are able to put all your time into this method without feeling burned out it is probably the best choice to focus most if not all your energy.

While automated vulnerability scanners can help you get experience in reporting and digging into specific CVEs (which I find to be the most fun so far and a good distraction when I am feeling stressed or burned out). Most bug bounty programs however frown on the use of vulnerability scanners as they really are heavy on traffic and they have likely already put their systems to these tests. If you do plan on using scanners be sure that the programs you choose don’t specify not using them and make sure you follow rate limits they require and add those headers whenever possible or they will block your ip and possibly go after your VPS! As always if anyone has any recommendations I would love to hear them!

## My Favorite Tools

Subfinder — https://github.com/projectdiscovery/subfinder The very best at passive subdomain finding. If you need to be more through of course you should use other tools as well such as amass and assetfinder. But when you want fast and effective this tool is amazing.

Nuclei — https://github.com/projectdiscovery/nuclei The most effective vulnerability scanner I have tried yet. Full of false positives and duplicates but lots of learning! Be sure to use headers and limited those rates as required or they will block and report you.

httpx — https://github.com/projectdiscovery/httpx There really isn’t much this tool cant do for you in recon. Its a fast and easy to use tool for getting titles, status codes, tech detection, probing for content, IP addresses and much more including many options for filtering results. Here is a helpful guide for those interested: https://www.hackingarticles.in/a-detailed-guide-on-httpx/.

## Recommended Resources 

Portswigger

https://www.pentesterlab.com/exercises

https://www.bugbountyhunter.com/

https://github.com/KingOfBugbounty/KingOfBugBountyTips

https://pentestbook.six2dez.com/
