---
title: "Kasm Workspaces"
date: 2021-12-28
description: "Kasm Workspaces"
menu:
  sidebar:
    name: Kasm Workspaces
    identifier: Kasm Workspaces
    weight: 6
---

### Introduction
Kasm Workspaces are delivered through Docker containers and is mainly aimed at small businesses looking to reduce platform resource requirements and increase security especially in this work from home space we have been moving into. But this software can easily have many personal uses with its disposable browsers and desktops. Options to host Kasm Workspaces include on-premise, in the cloud, or they even have the option to host it for you.

The most interesting feature to me is their Zero-Trust Browser Isolation which according to their website…

```
Web isolation moves the risk of browsing the web outside of the enterprise. 
All webinteractivity is executed in docker containers running in an isolated 
environment with only a seamless rendering user interface being sent to the 
user’s browser. Your users will feel as if they are experiencing the web 
firsthand, however, since web content never directly interacts with the 
user’s endpoint, your enterprise is protected against malware and your data 
remains safe.
```

The best thing is that this software is available for free and open source!

### Resource Requirements

Minimum Requirements

```
CPU - 2 cores   
Memory - 4GB        
Storage - 50GB (SSD) 
```

Supported Operating Systems

```
Ubuntu 18.04 / 20.04 (amd64/arm64)
Debian 9 / 10 / 11 (amd64/arm64)
CentOS 7 / 8 (amd64/arm64)
Oracle Linux 7 / 8 (amd64/arm64)
Raspberry Pi OS (Debian) 10 / 11 (arm64)
```

### Installation
As the most secure option I really wanted to find a cloud provider for this but with the minimum resource requirements I was looking at $20 a month so I decided to pass on that idea and looked at the next great option, Raspberry Pi. Unfortunately only Raspberry Pi 4 has the necessary resources and I didn’t have any and due to the chip shortage that seems to have no end in sight these devices now cost a lot and take months to get.

So in order to play around with this amazing software I loaded up the VirtualBox Ubuntu 18.04 machine I had, you can use any of the supported operating systems you are comfortable with or better yet…try something new and unfamiliar!

#### PreInstallation
So firstly what we actually want to do is create a swap partition. It is recommended to allocate 1 gigabyte per concurrent session you expect to run at any given time. Please adjust according to your needs.

```
sudo dd if=/dev/zero bs=1M count=1024 of=/mnt/1GiB.swap
sudo chmod 600 /mnt/1GiB.swap
sudo mkswap /mnt/1GiB.swap
sudo swapon /mnt/1GiB.swap
```

Verify that the swap file exists

```
cat /proc/swaps
```

To make the swap file available on boot

```
echo '/mnt/1GiB.swap swap swap defaults 0 0' | sudo tee -a /etc/fstab
```

#### Installation Guide
- Download the latest version of Kasm Workspaces to /tmp
- Extract the package and run the installation script.

```
cd /tmp
tar -xf kasm_release*.tar.gz
sudo bash kasm_release/install.sh
```

`Note: If you would like to run the application on a different port than the default 443 specifiy the port like so for the command above - sudo bash kasm_release/install.sh -L PORT`

Once the installation is complete the login details will be displayed. Save these somewhere safe for now.

Yes it really is as easy to install as that!

### Login
Access the Web Application running on port 443 at https://<WEBAPP_SERVER>

Log into the Web Application as the Administrator using the default credentials produced during the install. I suggest heading to the top right and selecting profile then reset password and changing the password to something more secure and saving that to a password manager.

Clicking on `Workspaces` at the top you can see a list of everything avaliable to launch in it’s own Docker container. If you wish to add something that isn’t already on the workspaces list, say `Kali linux `for example, head over to `Images` on the left side menu.

From there you can find the image you want or add your own. So for example if you wish to add Kali Linux you would find it on the list and click the ... then `Edit` and check the `Enabled` option then `Submit`. It will appear on the Workspaces page now but will take some time for it to setup before its ready to use.

There is also options for adding persistant data. See the guide for this at https://kasmweb.com/docs/latest/how_to/volume_mapping.html if needed.

By default this comes with a couple Desktop options already. While you do have the option to add custom images, the default Desktop does come with some great applications already installed. It has Microsoft Teams, Slack, Zoom, Visual Studio Code and Signal to name a few. These Desktops will stay open even when you log off Kasm, however if the desktop is killed by the Administrator the data in the container by default is lost. It is possible to map data out to a central server using the persistant data link above.

### Kasm - Open In Isolation
The main thing I absolutely love about Kasm is the Chrome extension which makes it easy to right click any link that might not be safe and open it in Kasm.

To do this yourself grab the extension from https://chrome.google.com/webstore/detail/kasm-open-in-isolation/pamimfbchojeflegdjgijcgnoghgfemn?hl=en-US.

Once you’ve added the extension to your browser click it and select `options`. Set the `Kasm Url` to the url of your machine and set whether you want it to open in a new tab or a new window and `Save`.

Now when you right click links you have the option to `Open link in Kasm`. When you are working within Kasm Workspaces there is always a menu on the left. When you are done with the session or think you encountered something suspecious you can delete the session there. Otherwise if you leave the session without deleting it then it will expire on its own after some time.

### Thoughts
Kasm is software that is based on Docker and provides a Web interface to run applications and desktops within a sandboxed container. `Note: As Kasm does run on a supported operating system, it is still your responsibility to ensure that the operating system itself is secure. `Sadly this amazing software wont likely see a whole lot of usage in its target audience as businesses tend to use Windows instead of Linux especially since thats what most users know how to use.

Honestly I love this and the single-server version is just great for home lab use! I can see great use for this especially with REMnux when I start getting into the reverse-engineering and analyzing malicious software side of security and I will continue to make use of the web isolation this provides.