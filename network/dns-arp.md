# Network — DNS & ARP

## In this file

- [ARP – Find Device by IP or MAC](#arp-find-device-by-ip-or-mac)
- [DNS Lookup – dig / nslookup](#dns-lookup-dig-nslookup)
- [ARP — Find Device IP or MAC on Network](#arp-find-device-ip-or-mac-on-network)
- [DNS Lookup and Traceroute](#dns-lookup-and-traceroute)
- [Find Savant Host on Network (arp / ping)](#find-savant-host-on-network-arp-ping)

---

## ARP – Find Device by IP or MAC

```bash
# Show full ARP table
arp -a

# Check a specific IP
arp <LOCAL_IP>

# Ping to populate ARP cache, then look up
ping -c 1 <LOCAL_IP> && arp <LOCAL_IP>

# On Linux (UDM shell):
ip neigh show
```

---

## DNS Lookup – dig / nslookup

```bash
# Quick lookup
nslookup hostname.local
nslookup hostname.local <UNIFI_GATEWAY_IP>

# Full dig output
dig hostname.local
dig @<UNIFI_GATEWAY_IP> hostname.local

# Reverse lookup (PTR)
dig -x <SAVANT_HOST_IP>
```

---

## ARP — Find Device IP or MAC on Network

```bash
# Show all ARP table entries:
arp -a

# Find Savant host by MAC prefix:
arp -a | grep -i 'd0:11:e5'

# Find any device by partial MAC:
arp -a | grep -i '1c:f6:4c'

# Ping to populate ARP table first (if device not showing):
ping -c 1 <LOCAL_IP>
arp -a | grep <LOCAL_IP>

# Network scan for active hosts:
nmap -sn <LOCAL_IP>/24

# Find hostname from IP:
nslookup <LOCAL_IP>
dig -x <LOCAL_IP>
```

---

## DNS Lookup and Traceroute

```bash
# DNS lookup:
dig google.com
nslookup <LOCAL_IP>

# Reverse lookup:
dig -x <LOCAL_IP>

# Check specific DNS server:
dig @8.8.8.8 google.com

# Traceroute:
traceroute <SAVANT_HOST_IP>
traceroute -n 8.8.8.8  # -n skips reverse DNS for speed

# Check default gateway/route:
netstat -rn
ip route show  # Linux
route -n get default  # macOS

# Flush DNS cache (macOS):
sudo dscacheutil -flushcache; sudo killall -HUP mDNSResponder
```

---

## Find Savant Host on Network (arp / ping)

```bash
# Find Savant host by MAC address (d0:11:e5 prefix):
arp -a | grep -i 'd0:11:e5'

# Ping by Bonjour hostname:
ping <SAVANT_HOST>.local
ping <SAVANT_HOST>.local

# Check which IP responds:
ping -c 1 <LOCAL_IP>
ping -c 1 <SAVANT_HOST_IP>
ping -c 1 <LOCAL_IP>

# mDNS lookup:
dns-sd -G v4 <SAVANT_HOST>.local
```

---
