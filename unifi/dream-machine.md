# UniFi — Dream Machine (UDM)

## In this file

- [SSH into UniFi Dream Machine](#ssh-into-unifi-dream-machine)
- [Check UniFi Network Interface Stats](#check-unifi-network-interface-stats)
- [SSH into UniFi Dream Machine Pro SE](#ssh-into-unifi-dream-machine-pro-se)
- [UniFi Network API — Dump Config (from UDM SSH)](#unifi-network-api-dump-config-from-udm-ssh)
- [UniFi — MAC Filtering on Switch Ports](#unifi-mac-filtering-on-switch-ports)
- [UniFi — Check Interface Stats from SSH](#unifi-check-interface-stats-from-ssh)
- [UniFi — WPA3 Enable + RADIUS MAC Auth](#unifi-wpa3-enable-radius-mac-auth)
- [UniFi — SSH Into UDM (Fix Password Issues)](#unifi-ssh-into-udm-fix-password-issues)

---

## SSH into UniFi Dream Machine

```bash
ssh root@<UNIFI_GATEWAY_IP>
```

---

## Check UniFi Network Interface Stats

```bash
# After SSH into UDM
cat /proc/net/dev
# or per-interface:
ifconfig eth4
# WAN throughput on UDM Pro SE:
vnstat -i eth8 --live
```

---

## SSH into UniFi Dream Machine Pro SE

```bash
ssh root@<UNIFI_GATEWAY_IP>
# or if on different subnet:
ssh root@<SAVANT_HOST_IP>

# If host key error (new hardware at same IP):
ssh-keygen -R <UNIFI_GATEWAY_IP>
ssh root@<UNIFI_GATEWAY_IP>
```

---

## UniFi Network API — Dump Config (from UDM SSH)

```bash
# From inside UDM SSH session:
# Step 1 — Login and get cookie:
curl -sk -X POST https://localhost/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username":"admin","password":"YOUR_UI_PASSWORD"}' \
  -c /tmp/cookies.txt

# Step 2 — Dump network config:
curl -sk https://localhost/proxy/network/api/s/default/rest/networkconf \
  -b /tmp/cookies.txt

# Alternative: Use API key (newer UniFi OS 4.x+):
curl -k -X GET 'https://<UNIFI_GATEWAY_IP>/proxy/network/integration/v1/sites' \
  -H 'X-API-KEY: <API_TOKEN>'
```

---

## UniFi — MAC Filtering on Switch Ports

```bash
# Via UniFi GUI:
# Devices → Select Switch → Port Manager → Click Port → Mode: Restricted by MAC ID → Add MACs

# Via 802.1X RADIUS MAC Auth (covers UDM built-in ports too):
# Settings → System → RADIUS → Enable Default RADIUS Server
# Add device: username=aabbccddeeff, password=aabbccddeeff (MAC no separators)
# Settings → Networks → Global Switch Settings → Enable 802.1X Control
```

---

## UniFi — Check Interface Stats from SSH

```bash
# Check interface config:
ifconfig | grep 'inet '

# Check active connections:
netstat -an | grep ESTABLISHED

# Check listening ports:
netstat -an | grep LISTEN

# Check routing table:
netstat -rn
```

---

## UniFi — WPA3 Enable + RADIUS MAC Auth

```bash
# Enable WPA3 on SSID:
# Settings → WiFi → Select SSID → Security → WPA3 (or WPA2/3 mixed)

# Enable RADIUS MAC Auth on WiFi:
# Settings → WiFi → Select SSID → Advanced → RADIUS MAC Authentication → Enable
# Point to Default RADIUS Server

# Verify RADIUS is running (from UDM SSH):
netstat -an | grep 7812
# RADIUS listens on port 7812/7813 (PPSK ports)
```

---

## UniFi — SSH Into UDM (Fix Password Issues)

```bash
# SSH user: root (always root, not admin or ubnt)
ssh root@<UNIFI_GATEWAY_IP>

# If 'Permission denied' or wrong password:
# SSH password is NOT the same as UniFi Network UI password
# It's set separately in UniFi OS Settings

# Fix 1: Reset SSH password in UniFi OS UI:
# unifi.ui.com → OS Settings (gear icon) → System → Advanced → SSH → Reset password

# Fix 2: Check SSH is enabled:
# OS Settings → System → Advanced → SSH → Enable toggle must be ON

# Fix 3: Clear cached host key:
ssh-keygen -R <UNIFI_GATEWAY_IP>
ssh root@<UNIFI_GATEWAY_IP>

# If still can't get in:
# Soft reset (keeps config): hold reset button 5-10 sec until LED flashes
# Then reconfigure SSH
```

---
