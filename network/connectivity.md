# Network — Connectivity Tests

## In this file

- [Port Connectivity Test (nc / curl)](#port-connectivity-test-nc-curl)
- [Traceroute – Path Diagnosis](#traceroute-path-diagnosis)
- [Ping – Basic Connectivity Check](#ping-basic-connectivity-check)
- [WireGuard — Fix Overlapping Subnet (Teleport/VPN)](#wireguard-fix-overlapping-subnet-teleport-vpn)
- [Port Connectivity Test (nc / curl / telnet)](#port-connectivity-test-nc-curl-telnet)
- [Ping — Connectivity Check](#ping-connectivity-check)

---

## Port Connectivity Test (nc / curl)

```bash
# Test if a port is open
nc -zv <SAVANT_HOST_IP> 8765
nc -zv <UNIFI_GATEWAY_IP> 12445

# With timeout
nc -zv -w 3 <SAVANT_HOST_IP> 8765

# Using curl for HTTP endpoints
curl -v --max-time 5 http://<SAVANT_HOST_IP>:8765/status

# Check what's listening locally
lsof -i -P -n | grep LISTEN
netstat -an | grep LISTEN
```

---

## Traceroute – Path Diagnosis

```bash
# macOS
traceroute 8.8.8.8

# Linux (UDM shell)
traceroute -n 8.8.8.8

# MTR (combined ping+traceroute)
mtr --report 8.8.8.8
```

---

## Ping – Basic Connectivity Check

```bash
# Quick reachability
ping -c 4 <UNIFI_GATEWAY_IP>
ping -c 4 <SAVANT_HOST_IP>

# With interval
ping -c 100 -i 0.2 <UNIFI_GATEWAY_IP>

# Ping with specific source interface
ping -I en0 <UNIFI_GATEWAY_IP>
```

---

## WireGuard — Fix Overlapping Subnet (Teleport/VPN)

```bash
# Problem: Both local LAN and remote LAN use <LOCAL_IP>
# Teleport doesn't work — local LAN route always wins

# Solution: Use WireGuard with NON-overlapping VPN subnet (<LOCAL_IP>/24)

# WireGuard client config (on MacBook):
# [Interface]
# Address = <LOCAL_IP>/24
# DNS = <UNIFI_GATEWAY_IP>
#
# [Peer]
# PublicKey = <server_pubkey>
# Endpoint = YOUR_PUBLIC_IP:51820
# AllowedIPs = <LOCAL_IP>/24, <LOCAL_IP>/24
# PersistentKeepalive = 25

# WireGuard server config (on remote network):
# [Interface]
# Address = <LOCAL_IP>/24
# ListenPort = 51820
# PrivateKey = <server_privkey>
#
# [Peer]
# PublicKey = <client_pubkey>
# AllowedIPs = <LOCAL_IP>/32

# Enable IP forwarding on WireGuard server (Linux):
sudo sysctl -w net.ipv4.ip_forward=1
# Persist:
echo 'net.ipv4.ip_forward=1' | sudo tee -a /etc/sysctl.conf

# Check routing table on Mac client:
netstat -rn | grep <LOCAL_SUBNET>
```

---

## Port Connectivity Test (nc / curl / telnet)

```bash
# Test if port is open:
nc -zv <SAVANT_HOST_IP> 12445
nc -zv <LOCAL_IP> 8084
nc -zv <LOCAL_IP> 1400

# Test with curl:
curl -I http://<LOCAL_IP>:1400/
curl -k -I https://<SAVANT_HOST_IP>:12445/

# Test telnet port:
telnet <DEVICE_IP> 4998

# Check what is listening locally:
lsof -i :8084
lsof -i :554
netstat -an | grep LISTEN | grep 8084

# Scan multiple ports on one host:
nmap -p 22,80,443,554,1400,4998,5060,8084,12445 <LOCAL_IP>
```

---

## Ping — Connectivity Check

```bash
# Basic ping:
ping -c 4 <SAVANT_HOST_IP>
ping -c 4 <LOCAL_IP>
ping -c 2 <LOCAL_IP>

# Ping with specific size:
ping -c 4 -s 1400 <UNIFI_GATEWAY_IP>

# Continuous ping until Ctrl+C:
ping <SAVANT_HOST_IP>

# Ping sweep (check which IPs are alive):
for i in $(seq 1 254); do ping -c 1 -W 1 <SUBNET_PREFIX>.$i &> /dev/null && echo "<SUBNET_PREFIX>.$i is up"; done
```

---
