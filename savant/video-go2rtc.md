# Savant — Video / go2rtc

## go2rtc — Install for Video Feed Bridging

```bash
# Download and install go2rtc (architecture-aware)
ARCH=$(uname -m)
if [ "$ARCH" = "arm64" ]; then
  URL="https://github.com/AlexxIT/go2rtc/releases/latest/download/go2rtc_mac_arm64"
else
  URL="https://github.com/AlexxIT/go2rtc/releases/latest/download/go2rtc_mac_amd64"
fi

curl -L "$URL" -o /usr/local/bin/go2rtc
chmod +x /usr/local/bin/go2rtc

# Verify
go2rtc --version

# Run with config
go2rtc -config /Users/Shared/go2rtc.yaml
```

---

## go2rtc — Config File + Run

```bash
# Create config on Savant host:
cat > ~/go2rtc.yaml << 'EOF'
streams:
  package_camera:
    - rtsps://<LOCAL_IP>:7441/xywRVdXGY9eESrDJ?enableSrtp
EOF

# Run go2rtc (listen on port 554 — requires sudo for port <1024):
sudo ~/go2rtc -config ~/go2rtc.yaml

# Run on port 8554 (no sudo needed):
~/go2rtc -config ~/go2rtc.yaml
# Then set listen port in config:
# api:
#   listen: ":1984"  # web UI
# rtsp:
#   listen: ":554"   # or ":8554" for non-root

# Check go2rtc process:
ps aux | grep go2rtc | grep -v grep

# View logs:
tail -n 40 /tmp/go2rtc.log
# or:
sudo tail -f /var/log/go2rtc.log

# Stop go2rtc:
sudo pkill go2rtc
```

---
