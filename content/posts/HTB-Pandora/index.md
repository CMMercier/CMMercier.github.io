---
title: "HTB: Writeup — Pandora"
date: 2022-05-24
description: "HTB: Writeup — Pandora"
menu:
  sidebar:
    name: "HTB: Writeup — Pandora"
    identifier: "HTB: Writeup — Pandora"
    weight: 11
---

## Recon

### Initial nmap scan

An initial scan with nmap shows that there is two ports open, ssh on 22 and http on 80.

![image](https://user-images.githubusercontent.com/43668197/170124664-ab88f6b4-7047-4284-998d-288f8037d39a.png)

### Site

Having a look at the page hosted on port 80 there appears to be a host name of Panda.HTB.

![image](https://user-images.githubusercontent.com/43668197/170124707-2510680b-01ef-4241-9ae7-ad384e0e0842.png)

Next I added this host to the `/etc/hosts/` file with my favorite editor nano. The line added to hosts should look like `10.10.11.136 Panda.HTB`. Oddly the same page loads so there's nothing new to see here.

### Directory Enumeration

While walking the site its always best to run a directory scan with your favorite tool in the background. This one only returned the directory `/assets`

![image](https://user-images.githubusercontent.com/43668197/170124747-06d9a802-df6d-47b4-b9ec-b4346de3f793.png)

But there wasn’t much to see here.

![image](https://user-images.githubusercontent.com/43668197/170124777-726d5bfb-4a5a-4782-a82e-9840b717a189.png)

### NMAP UDP

Now it’s time to run a nmap scan to check all ports, but that came back with nothing. It never hurts to enumerate all the things while looking through the site some more so be sure to check at least the top UDP ports. Here I found that snmp port 161 is open!

![image](https://user-images.githubusercontent.com/43668197/170124807-77bfbc55-9cd2-4714-a9bb-1a29efbf7833.png)

### SNMP - UDP PORT 161

As the only finding so far it is time to install some tools to check it out.

`apt install snmp snmp-mibs-downloader`

Then I ran:

`snmpbulkwalk -Cr1000 -c public -v2c 10.10.11.136 > snmp-pandora`

I got all kinds of information back from this. Running processes, network information, host information, installed packages and paths.

![image](https://user-images.githubusercontent.com/43668197/170124847-d5b892d8-2ca3-462b-b8f3-5bed9945212b.png)

## Initial Foothold

There is a lot to look through but the process running a script at `/usr/bin/host_check` stands out. This is where saving the results comes in handy as I can simply grep things that look interesting and see the result.

![image](https://user-images.githubusercontent.com/43668197/170124947-c8bec222-25f5-40e2-b42f-1b06ed72ae69.png)

And here I found a username `daniel` and password `HotelBabylon23`! Time to try them out on ssh since the earlier scan showed that port is open.

![image](https://user-images.githubusercontent.com/43668197/170124973-f53b5aa9-5867-42ae-9f0f-4bf37571f7ff.png)

Now that I am in its time to have a look around. There is nothing in Daniel’s home directory so I checked if there was any other users under home and sure enough there is a user named matt and matt has the`user.txt` flag but I am unable to read it just yet.

 `cat: user.txt: Permission denied`

Next I checked `sudo -l` but Daniel has no permissions to for sudo. So I went and checked if there was anything interesting in the webserver config files.

![image](https://user-images.githubusercontent.com/43668197/170125027-06127e81-fceb-4549-ad57-0b52aea14351.png)

Here I found another virtual host mention by pandora.panda.htb. As this is an internal host I had to forward it through ssh.

`ssh daniel@10.10.11.136 -L 8888:localhost:80`

And now I have access the site locally.

![image](https://user-images.githubusercontent.com/43668197/170125068-de3cb2c2-b8cb-47dc-89cd-67150dd515ab.png)

### CVE-2021-32099

I was immediately greeted with a Pandora FMS login page which nicely includes the version information at the bottom! A quick search reveals that this version has a vulnerability, CVE-2021-32099.

Reference:

[https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-32099](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-32099)

With this I gave sqlmap a quick attempt but as usual it found the injection but doesn’t exploit it.

`sqlmap -u '[http://127.0.0.1:8888/pandora_console/include/chart_generator.php?session_id=1](http://127.0.0.1:8888/pandora_console/include/chart_generator.php?session_id=1)' --batch`

![image](https://user-images.githubusercontent.com/43668197/170125132-633d5947-7ee2-47eb-bcb2-ac7fe55d082d.png)

I could however enumerate the database using `--dbs`

![image](https://user-images.githubusercontent.com/43668197/170125166-bc583d7a-83e3-43d9-a8bb-89f2dc31c480.png)

Checking the pandora tables with `-D pandora --tables` gives us a long list of 178 tables!

![image](https://user-images.githubusercontent.com/43668197/170125232-3ed9a03a-f206-4f5f-aa83-eecaff4fd51e.png)

The most obvious table to check tpassword_history didn’t seem to reveal anything or at least I was unable to crack the password. `-D pandora -T tpassword_history --dump`

![image](https://user-images.githubusercontent.com/43668197/170125286-d69b4f90-d702-4652-9e68-252fb8c5d86d.png)

But in tsessions_php there was a list of 55 cookies. As I only need access to matt or admin accounts I ignored the NULL and Daniel cookies. Since the user.txt flag is located under matt I tried his cookie in the browser first and got access!

![image](https://user-images.githubusercontent.com/43668197/170125322-ce14ea7d-9ef6-4b9c-a171-3782bb31864d.png)

![image](https://user-images.githubusercontent.com/43668197/170125349-d96daf60-a984-4ba5-b378-fc1bcbd630ff.png)

### CVE-2020-13851

From here I found another known vulnerability that I could use, `CVE-2020-13851` to perform RCE.

Reference:

[https://www.coresecurity.com/core-labs/advisories/pandora-fms-community-multiple-vulnerabilities](https://www.coresecurity.com/core-labs/advisories/pandora-fms-community-multiple-vulnerabilities)

From the console I went to `Events` and `View Events` and captured this request in burp which I then send over to repeater. I then modified the payload and started nc to be ready before sending the request.

![image](https://user-images.githubusercontent.com/43668197/170125397-d5c4699e-1c3b-40d2-af0f-6cba5550d760.png)

![image](https://user-images.githubusercontent.com/43668197/170125430-3cb8325a-697c-4256-9559-7c30947491e5.png)

And I am in as the user matt and can cat out the user flag in matt’s home directory!

## Privilege Escalation

Nothing else in their directory so I try sudo -l with no luck. But the next thing to check for is SUID binaries `find / -perm -4000 -ls 2>/dev/null` and something interesting comes up

### SUID

`/usr/bin/pandora_backup`

![image](https://user-images.githubusercontent.com/43668197/170125487-be23f8a4-17bf-45a4-ae97-8530e8cfd355.png)

Strangely this fails and the connection keeps dropping so I dropped my public key in `/home/matt/.ssh/authorized_keys` and got a stable shell over ssh instead. This also made it so running pandora_backup now works just fine!

![image](https://user-images.githubusercontent.com/43668197/170125518-c0032185-f2dd-4d7f-9718-8f7b71e6e863.png)

Tar is mentioned a few times here and has no path given. It may be susceptible to path hijack so I’ll use `export PATH**=**/dev/shm:$PATH` which makes the first place tar looks in as /dev/shm.

Time to make a payload `nano tar`

```jsx
#!/bin/bash

bash
```

Then I changed permissions to execute`chmod +x tar` and reran `pandora_backup` again and it changed to root shell and all that is left is to go claim that root.txt flag!

![image](https://user-images.githubusercontent.com/43668197/170125581-8fa39af5-8408-4504-9f31-3deeebaf6c79.png)
