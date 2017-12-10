# Once again about Mikrotik Adblock

Periodical glitches of my home WI-FI all-in-one router finally put me in front of two decisions:
- use a separate reliable router
- use a separate reliable access point

I choose MikroTik hEX RB750Gr3 as a home router because it is relatively cheap (~60$), stable in work (according to reviews) and can do some basic advertising content filtering.


Small, but functional, it cannot do deep packet inspections (DPI), but has capabilities to implement simple adblock solution using DNS — we specify addresses of advertising domains on router as loopback 127.0.0.1. There are a lot of articles about “Mikrotik adblock” but most of them offer a periodic download of a device-executable script that runs on your device. This looks a bit scaring because you do not control the content of these scripts. I prefer to manually download a script, upload it to a router and run it on update and while the router is loading/when the router has loaded. Let’s see how to do it.


After MikroTik hEX RB750Gr3 starts it gets a public IP from WAN and does NAT for internal network 192.168.88.0/24. The web-admin interface called “WebFig” is available at http://192.168.88.1. After logging in to web-admin, you definitely should change admin password. So, it is not hard to install a/the device/devices.
Ok, set the router as a DNS server in DHCP Server properties: go to IP/DHCP Server menu, Network tab, open a/the default record. Input 192.168.88.1 in DNS Servers field.

[!Mikrotik DHCP](2017-11-21-mikrotik-adblock/mikrotik_dhcp.png)

Now download adblock script for Mikrotik from https://www.micu.eu/adblock/adblock.php and look at it till the end.

```
…
/ip dns static
add address=127.0.0.1 name=localhost
add address=127.0.0.1 name=s1.2mdn.net
add address=127.0.0.1 name=s0.2mdn.net
…
```

Looks good, upload it to the device using Files menu.

[!Mikrotik Files](2017-11-21-mikrotik-adblock/mikrotik_files.png)

Add this script to import previously uploaded script, then run it.

```
:log info “Adblock: apply start”
/ import file-name=adblock.rsc
:log info “Adblock: apply finished”
```

[!Mikrotik Schedule](2017-11-21-mikrotik-adblock/mikrotik_schedule.png)

Use the System/Scheduler menu to add a scheduler to run import script on device start .

Periodically do a manual adblock script update.
Now I see a lot of displeased smiles and smile.

[!YouTube Adblocked](2017-11-21-mikrotik-adblock/youtube_adblocked.png)

That’s all! Happy surfing!