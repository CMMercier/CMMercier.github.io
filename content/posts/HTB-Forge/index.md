---
title: "HTB: Writeup — Forge"
date: 2022-01-27
description: "HTB: Writeup — Forge"
menu:
  sidebar:
    name: "HTB: Writeup — Forge"
    identifier: "HTB: Writeup — Forge"
    weight: 6
---

## Recon

![20220523134231851](img-paste-20220523134231851.png)

Using Rustscan and Nmap came back with different results. We can’t really do anything with the filtered port but it’s nice to know that it is there. I wasn’t able to find a way to make Rustscan discover this filtered port that Nmap had so I may default back to using nmap. Or just keep trying them both out like I have been.

So here we have 3 open ports. The 2 standard ports on 22:ssh and 80:http and a filtered ftp port on 21. Again we cannot do anything on the filtered port so it is time to check out the only option, the website.

![20220523134259662](img-paste-20220523134259662.png)
![2022052313431257](img-paste-2022052313431257.png)

Immediately we notice the option to Upload an image. Trying to upload a php reverse shell comes back with:

![2022052313432978](img-paste-2022052313432978.png)

Trying again with curl shows our script is there but it wasn’t run. Maybe this server isn’t running php. Running a netcat listener and submitting my own ip on the site shows that this is infact running Python.

Running ffuf immediately comes back with an interesting subdomain:

![20220523134349905](img-paste-20220523134349905.png)

Adding this new discovery to /etc/hosts and attempting to visit it gives us this message:

`Only localhost is allowed!`

Attempting to upload a file from url with the url http://admin.forge.htb tells us this is blacklisted.

![20220523134429184](img-paste-20220523134429184.png)

I was able to bypass this silly blacklist by simply using http://ADMIN.FORGE.HTB instead. Using curl on the link it provides reveals another directory /announcements.

![20220523134442423](img-paste-20220523134442423.png)

Now to get a new link by uploading from the url http://ADMIN.FORGE.HTB/announcements and using curl on that.

![20220523134454223](img-paste-20220523134454223.png)

We get the internal ftp server credentials of user:heightofsecurity123!. Seems filtered ports aren't always useless to know about but we would have found out about it this way even if I hadn't used nmap.

Time to try http://ADMIN.FORGE.HTB/upload?u=ftp://user:heightofsecurity123!@ADMIN.FORGE.HTB and curl that url responce. And we get the directory with user.txt so all we have to do to get the flag from user.txt is to add it to the end of the url. http://ADMIN.FORGE.HTB/upload?u=ftp://user:heightofsecurity123!@ADMIN.FORGE.HTB/user.txt

![2022052313451039](img-paste-2022052313451039.png)
![20220523134519500](img-paste-20220523134519500.png)

## Initial Foothold

To get the initial foothold onto the system all we have to do now is upload from the url again but this time to http://ADMIN.FORGE.HTB/upload?u=ftp://user:heightofsecurity123!@ADMIN.FORGE.HTB/.ssh/id_rsa

![20220523134537947](img-paste-20220523134537947.png)


nano id_rsa and paste the private key info in there and don't forget to chmod 600 id_rsa. And we are in!

![20220523134554206](img-paste-20220523134554206.png)

## Privilege escalation

First thing we always do once we gain user access is to check what sudo permissions that user has.

![2022052313461124](img-paste-2022052313461124.png)

The user has sudo access to running a specific python script so time to see what this script does and how we can use it to gain root access.

![20220523134626611](img-paste-20220523134626611.png)

We get the password secretadminpassword. I ran the script and found that it is listening on port 48888.

![20220523140804744](img-paste-20220523140804744.png)

![20220523140814327](img-paste-20220523140814327.png)

This brings up a menu. If you enter invalid information into the menu it brings up a Pdb debugger.

![20220523140832545](img-paste-20220523140832545.png)

Now we can use Pdb debugger to get root access with the following commands:

```
(Pdb) import os
(Pdb) os.system ('chmod u+s /bin/bash')
(Pdb) exit
user@forge:~$ /bin/bash -p
```

![20220523140854580](img-paste-20220523140854580.png)

Just cat out /root/root.txt and machine complete! This felt easier than some of the “easy” machines and I enjoyed the Web and SSRF learning involved.
