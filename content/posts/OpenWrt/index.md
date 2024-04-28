---
title: "OpenWrt on Raspberry Pi 3b"
date: 2021-12-28
description: "OpenWrt on Raspberry Pi 3b"
menu:
  sidebar:
    name: OpenWrt on Raspberry Pi 3b
    identifier: OpenWrt on Raspberry Pi 3b
    weight: 4
---

### Introduction
So back when Raspberry Pi was super hyped I really wanted one. I always loved to play with tech and there was so much that could be done with such a handy small and inexpensive device.

So eventually and very excitedly I purchased the Raspberry Pi 3 when I had some extra cash to do so. But after an extensive search of existing Pi project lists, personal sites, and Youtube videos, I wasn’t really able to find many project or project ideas that actually interested me or felt worthy.

First I tried out the famous Pi-hole. It was fun to setup and watch it work and it does indeed work rather well but it felt like a waste to just use the device for that. As I was interested in security I started to wonder if I could use this little device for IDS/IPS and actually found existing guides for installing and configuring Snort on Raspberry Pi.

In my excited haste I was able to get Snort all setup and running before finding out that the Raspberry Pi 3 does not have the CPU or Memory required. In my disappointment I placed the Pi away in a drawer for some years before I recently ran across this video by NetworkChuck: https://www.youtube.com/watch?v=jlHWnKVpygw.

I immediately grabbed that old Raspberry Pi 3b out and got to work installing and playing with OpenWrt on it. So, you might be wondering…why OpenWrt?

While NetworkChuck’s idea of using it as a travel router for security purposes really interested me and I do plan on using it as such when I do travel. My main use for it currently is subnetting certain systems on my network from others while providing these wireless devices with an adblock at the router level. Some of these devices being rather difficult or impossible to get adblocks onto themselves. I also have a vpn setup on the OpenWrt but I am currently using the free ProtonVPN instead of the nordVPN that NetworkChuck uses.

### Installation
Firstly, you will need to find your Raspberry Pi model and version and download the OpenWrt image from https://openwrt.org/toh/raspberry_pi_foundation/raspberry_pi.

Then install that operating system onto your Raspberry Pi’s microSD card. Personally I enjoy using the simple Raspberry Pi Imager from https://www.raspberrypi.com/software/.

Insert the microSD back into your Raspberry Pi once the Imager is complete and connect your Pi to a laptop or pc using an Ethernet cable.

By default the OpenWrt gateway will be 192.168.1.1 which you should be able to access immediately but if you run into any issues connecting (remember to give the Pi some time if you recently booted it) then you might need to set your pc IP to an IP on the network like 192.168.1.10.

Next you will want to use SSH to access and configure your new Pi router.

`ssh root@192.168.1.1`

As you might have noticed, it does not prompt you for a password which is super insecure and you should definitely change this first by running:

`passwd`

Enter a new super secure password of your choosing!

Now its time to edit some configurations.

`cd /etc/config`

You can create backups of the config files first if you like by doing:

```
cp firewall firewall.bk
cp network network.bk
cp wireless wireless.bk
```

It would take seconds to redo the whole process thus far if something did go wrong so while this backup isn’t necessary it is good to get into the habit of creating backups before making changes!

Currently we don’t have access to nano so we have to use vi to edit the config files. Which as much as we all want to cry at having to use vi it is good to learn or refresh on its use as this isn’t likely the only time you will experience an environment in which you are forced to use it.

`vi network`

Where it says:

```
config interface 'lan'
    option device 'br-lan'
    option proto 'static'
    option ipaddr '192.168.1.1'
    option netmask '255.255.255.0'
    option ip6assign '60'
```

Change option `ipaddr '192.168.1.1'` to something of your choosing that isn’t so commonly used. NetworkChuck for example used `option ipaddr '10.71.71.1'` so I will use this example going forward. Also add the line:

`option force_link '1'`

After `option ip6assign '60'`. At the bottom we will add a new interface:

```
config interface 'wwan'
    option proto 'dhcp'
    option peerdns '0'
    option dns '1.1.1.1 8.8.8.8'
```

If you wish to setup a vpn to use with this then there is one more interface you will need to add to the bottom:

```
config interface 'vpnclient'
    option ifname 'tun0'
    option proto 'none'
```

Now to save and exit from vi simply hit `esc` then type in `:wq` and hit `enter`.

The next thing we need to configure is the firewall.

`vi firewall`

Where it says:

```
config zone
    option name       wan
    list network      'wan'
    list network      'wan6'
    option input      REJECT
    option output     ACCEPT
    option forward    REJECT
    option masq       1
    option mtu_fix    1
```

Simply change `option input REJECT` to `option input ACCEPT`

Now to save and exit from vi simply hit esc then type in :wq and hit enter.

Time to reboot by using the command:

`reboot`

You will get kicked out of the SSH session at this time. If you had to change your network adapter settings previously you will now have to go back and set it to obtain ip automattically again before continuing.

Give it some time to reboot if you didn’t have to go change settings and maybe enjoy some coffee or some tea while waiting. SSH back with the IP and password you set.

`ssh root@10.71.71.1`

So now we must get the usb wifi setup. For this we will need internet access to download the drivers.

`vi /etc/config/wireless`

Make a few changes to the first config so it looks exactly like so:

```
config wifi-device 'radio0'
    option type 'mac80211'
    option channel '7'
    option hwmode '11g'
    option path 'platform/soc/fe300000.mmcnr/mmc_host/mmc1/mmc1:0001/mmc1:0001:1'
    option htmode 'VHT80'
    option disabled '0'
    option short_gi_40 '0'
```

Now to save and exit from vi simply hit esc then type in :wq and hit enter.

Update the wireless interface configuration.

```
uci commit wireless
wifi
```

Open a web browser to access the OpenWrt GUI by going to the IP 10.71.71.1 or whatever you choose for your IP.

After you login select `Network` and `Wireless` and then click `Scan`. You will need to select your wireless network from the list and on the new window that pops up make sure `Replace wireless configuration` is selected and put in your `WPA passphrase`. Select `Submit`, then `Save` and lastly `Save and Apply`.

Go back to your command prompt and `ping google.com` just to make sure the connection is working.

Now that your Raspberry Pi has internet its time to update using:

`opkg update`

If it fails reboot and run it again and it should work. After update is successfully completed its time to install usb drivers along with openvpn and nano. If you do not wish to use openvpn or nano simply do not include those commands.

```
opkg install kmod-rt2800-lib kmod-rt2800-usb kmod-rt2x00-lib kmod-rt2x00-usb kmod-usb-core kmod-usb-uhci kmod-usb-ohci kmod-usb2 usbutils openvpn-openssl luci-app-openvpn nano
```

Connect your wireless usb now and use `lsusb` and check that the wireless usb is listed there.

Now we must edit the wireless config with the editor of your choice:

`nano /etc/config/wireless`

Under `config wifi-device 'radio1'` set `option disabled '1'` to `option disabled '0'`

Then under `config wifi-iface 'default_radio1'` change `option ssid 'OpenWrt'` to an ssid of your choosing. Change `option encryption 'none'` to `option encryption psk2`. And add another option line under it `option key 'EnterStrongKeyHere'` and enter your own strong psk2 key.

Save and exit. If using nano its `ctrl x y and enter`.

Update the wireless interface configuration.

```
uci commit wireless
wifi
```

Connect to the new ssid you created for the wifi and make sure its giving internet.

Configurations! You’ve now got your very own Raspberry Pi Router!

### Setting up Ad Blocking
OpenWrt comes with its own additional service for Ad Blocking. The setup is rather basic thankfully.

```
# Install packages
opkg update
opkg install adblock

# Provide web interface
opkg install luci-app-adblock

# Backup the blocklists
uci set adblock.global.adb_backupdir="/etc/adblock"

# Save and apply
uci commit adblock
/etc/init.d/adblock restart
```

### Setting up VPN
I set this up using the free ProtonVPN. If you dont already have an account with them and would like to use them on your Raspberry Pi device go ahead and create an account now on their website.

Once you are logged in head over to the `Downloads` section and scroll down to the `OpenVPN configuration files` section. Here you want to select `Router` for platform and leave the protocol as `UDP`. Scroll down and decide which server you want to use and click `Download` on that server.

Next load up your OpenWrt GUI at 10.71.71.1 (or whatever you set your ip to) and login. At the top select `VPN` and `OpenVPN`. Now where it says `OPVN configuration file upload` give it a name and click `Choose File`. Select the file that you just downloaded and click `Upload` then `Save & Apply`.

It should now appear on the list above. Check the `Enabled` box and click `start` then `Save & Apply`.

At this point you will lose connection to the internet due to the firewall. I found that the best way to do this part was actually to follow NetworkChuck’s commands:

```
uci rename firewall.@zone[0]="lan"
uci rename firewall.@zone[1]="wan"
uci del_list firewall.wan.device="tun+"
uci add_list firewall.wan.device="tun+"
uci commit firewall
/etc/init.d/firewall restart
```

You should have access to the internet once again. Go ahead now and make sure your connection is indeed running through the vpn.

### Conclusion
This has been my favorite Raspberry Pi project by far. While the rest were fun and a learning experience in themselves, they lacked the functionality to feel truly satisfying once complete.

The only downside to this project has been how truly simple it is to set all this up. There was very little issue that I ran into and had to do my own research for. So now my focus has been on what other capabilities and features OpenWrt has to play around with and continue to learn!

I hope this guide has been interesting enough for you to give this project a try and discover all the great uses a Raspberry Pi Router can have in your life too!
