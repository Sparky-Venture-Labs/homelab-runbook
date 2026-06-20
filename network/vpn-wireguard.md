# Network — VPN & WireGuard

## WireGuard — Check Tunnel Status

```bash
# Check WireGuard interfaces (macOS):
ifconfig | grep -A3 utun

# Check routing table for VPN routes:
netstat -rn | grep <LOCAL_SUBNET>
netstat -rn | grep 10.6

# Check if traffic is going into tunnel:
ping -c 1 <LOCAL_IP>  # if WireGuard is routing this, it goes to remote

# On WireGuard server (Linux):
wg show
wg show wg0

# Check IP forwarding on WireGuard server:
sysctl net.ipv4.ip_forward
# Should return: net.ipv4.ip_forward = 1
```

---
