---
title: "Beginner Bug Bounty Journey"
date: 2022-02-23
description: "Beginner Bug Bounty Journey"
menu:
  sidebar:
    name: Beginner Bug Bounty Journey
    identifier: Beginner Bug Bounty Journey
    weight: 7
---

## Introduction

I began my journey with all the many resources out there both free and paid that are centered on training you to become a penetration tester. The main resources I found to be the best for learning were TryHackMe and the Practical Ethical Hacking course by TCM Academy. The problem I learned mostly by following the major community leaders in Youtube, LinkedIn and Twitter, is that there are already far more people wanting to be penetration testers than there are open roles for them to fill. But this was alright as these courses still taught the basics required for most other Cyber Security roles.

The problem I actually had was finding myself quickly overwhelmed by the sheer amount of materials available and how much more in depth I wanted to learn. I needed somewhere to begin my focus on so I decided to go back to the Practical Ethical Hacking course curriculum and choose a section from there to dig deeper into. As you can probably guess from the title, the Web Application Enumeration and Top 10 Web Application Vulnerabilities are what stood out to me. I knew that the Bug Bounty scene had been gaining a lot of popularity lately and it gave me the opportunity to not only learn a lot but possibly make a little side cash to help fund more certifications or fun projects.

## The Beginner Methodology
Initially and somewhat shamefully my first response, since my web application knowledge is limited to a web application programming course taken in uni and a little bit in TryHackMe and OWASP Juice Shop, was to find tools and scan ALL the things. I thought that maybe I could find some things from them and learn from there but it is highly unlike to find low hanging fruit especially on bug bounty programs which have already been scanned by many others and this wasn’t going to be nearly as easy as intentionally vulnerable applications.

Doing this did however lead me to a couple auto recon tools that I do find to be very helpful. reconftw which you can find over at https://github.com/six2dez/reconftw and Sn1per which you can find at https://github.com/1N3/Sn1per. They handle a lot of the very helpful initial recon for me. Another amazing resource is the Pentesting Web checklist by six2dez found at https://github.com/six2dez/pentest-book/blob/master/others/web-checklist.md. The recon tools can cover a good amount of this checklist but I prefer to also manually go through each part and try out the different tools and methods myself. Also while the tools are running it is very important to have `OWASP ZAP` and `Burp` open while manually crawling through the websites. I have personally found out a lot more about the sites by doing this.

So at first I was scanning around different bounty programs and while this might be the faster way of finding something, I didn’t really feel like I was learning anything. So I decided to pick one program and stick to it. Most importantly my goal has been to learn everything I can about their tech stacks. Especially their versions and known vulnerabilities the software has or has had in the past.

It is also important to learn about these web application vulnerabilities, so I have also been reading Real-World Bug Hunting: A Field Guide to Web Hacking by Peter Yaworski and after reading about each vulnerability I have been testing them out on my bug bounty program of choice. I do want to note that I did read over this book before I even started on this journey but it honestly didn’t make a whole lot of sense to me then. I understand a lot of it much clearer now and I will likely end up reading over it again at least once more in the future as my understanding grows. So if the material that you look up now doesn’t make sense do not give up! You will get there!

## Next Steps
One of the things I have been working on is growing my LinkedIn and Twitter communities. Especially Twitter is a great way to learn about new things and keep up with current happenings but it’s also been a great resource for finding out about course discounts or giveaways! I tend to spend a half hour or hour before bed reading over posts.

That being said I plan to take a break from that unless I have the extra time and instead make it my priority to spend that particular time reading over reports on HackerOne. The Hactivity section is a great way to find the latest disclosures and reading the reports is probably going to be the best way to learn as they often include the disclosed report. I also plan to get back to the free amazing web security training from PortSwigger Academy located over at portswigger.net/web-security.

Hopefully the next time I write it will be to talk about a bug I found and how I accomplished the finding. But if not I will not be discouraged as my goal and my passion is learning and I have learned so much already! Remember, bug bounty hunting is free hands-on pentesting experience!
