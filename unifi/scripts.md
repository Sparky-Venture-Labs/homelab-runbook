# UniFi — Full Scripts

## Diagnose Subnet Overlap (Teleport / VPN)

```bash
# Show all routing table entries
route -n get default
netstat -rn

# Show IP addresses on all interfaces
ip addr show          # Linux (UDM shell)
ifconfig              # macOS / BSD

# Find which interface owns a specific IP
ip route get <UNIFI_GATEWAY_IP>
```

---

## SSH Key Setup — Passwordless Auth

```bash
# Generate SSH key (if not exists):
ssh-keygen -t ed25519 -C "sparky@macbook"

# Copy key to Savant host:
ssh-copy-id RPM@<LOCAL_IP>
# Enter password once — never needed again

# Copy to UDM:
ssh-copy-id root@<UNIFI_GATEWAY_IP>

# Manual method (if ssh-copy-id not available):
cat ~/.ssh/id_ed25519.pub | ssh RPM@<LOCAL_IP> 'mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys'

# Verify:
ssh RPM@<LOCAL_IP> 'echo success'  # should NOT prompt for password
```

---

## Teleport VPN — Overlapping Subnet Workarounds

```bash
# Problem: Both local and remote LANs use <LOCAL_IP>/24
# Teleport fails — local LAN route always wins

# Option 1 (BEST): Renumber one LAN
# - Remote site: change to <LOCAL_IP>/24 or 10.x.x.0/24
# - Or local: change to <LOCAL_IP>/24
# - Then Teleport routes distinct subnets correctly

# Option 2: WireGuard with controlled subnet
# (See 'WireGuard — Fix Overlapping Subnet' entry)

# Option 3: Jump host on different subnet
# - Deploy small VM at remote site on <LOCAL_IP>/24
# - VPN into <REMOTE_SUBNET>, then SSH from there to <LOCAL_IP>
# - Uses local address from inside the remote LAN

# Option 4 (RISKY): Edit UniFi MongoDB for Teleport subnet
# Unsupported, breaks on updates — avoid

# Check current routing table:
netstat -rn
# Look for <LOCAL_IP>/24 — which interface does it route to?
# en0/en1 = local NIC (bad — traffic stays local)
# utun0/utun1 = VPN tunnel (good — traffic goes remote)
```

---
