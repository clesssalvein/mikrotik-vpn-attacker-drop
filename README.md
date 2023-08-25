# Description

Mikrotik RouterOS script, which monitors VPN attackers, and blocks their IPs. So far it only blocks PPTP.

**This is not a 100% way to drop attackers. But it works well in most cases.**

# Requirements

- Mikrotik router (RouterOS v6.x)

# Installation

Add script

```
/system script add name=vpnAttackerDrop
```

Edit script

```
/system script edit name=vpnAttackerDrop source
```

Paste into it the content of the vpnAttackerDrop.script

Add scheduler task

```
/system scheduler add interval=1m name=vpnAttackerDrop \
on-event=vpnAttackerDrop start-time=startup
```

Add firewall rules

```
/ip firewall filter
add action=drop chain=input comment="=common2= drop_pptp_1723_port_for_vpnAttacker" \
dst-port=1723 protocol=tcp src-address-list=vpnAttackerDrop
```

# How it works

- Script vpnAttackerDrop is looking for a line "\<xxxx\>: \<user\> tunnel authentication failed" in the logs
- If it found something, then it looks at the log line above
- If there's a line with contents like "TCP connection established from x.x.x.x" above, it is looking for an IP-address in it and put it into the address-list "vpnAttackerDrop"
- Firewall rules drop attackers' IP-addresses
