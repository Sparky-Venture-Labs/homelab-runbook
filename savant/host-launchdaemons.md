# Savant — Host & LaunchDaemons

## In this file

- [Reload / Restart Savant LaunchDaemon](#reload-restart-savant-launchdaemon)
- [SSH into Savant Host Processor](#ssh-into-savant-host-processor)
- [Plutil – Validate LaunchDaemon Plist](#plutil-validate-launchdaemon-plist)
- [LaunchDaemon — Load / Unload / Restart](#launchdaemon-load-unload-restart)
- [Find & Kill Process by Name (macOS)](#find-kill-process-by-name-macos)
- [go2rtc — Install on Savant Host (Apple Silicon Mac)](#go2rtc-install-on-savant-host-apple-silicon-mac)
- [Savant Host — Python Environment Check](#savant-host-python-environment-check)
- [launchctl — bootout/bootstrap (Modern macOS)](#launchctl-bootout-bootstrap-modern-macos)
- [go2rtc — LaunchDaemon (Auto-Start on Savant Host)](#go2rtc-launchdaemon-auto-start-on-savant-host)
- [Verify All LaunchDaemons Running](#verify-all-launchdaemons-running)
- [Python 3 — Quick Script Template for Savant Host](#python-3-quick-script-template-for-savant-host)

---

## Reload / Restart Savant LaunchDaemon

```bash
sudo launchctl unload /Library/LaunchDaemons/com.savant.doorbell.plist
sudo launchctl load /Library/LaunchDaemons/com.savant.doorbell.plist

# Check if running
launchctl list | grep savant

# Tail logs
tail -f /Users/Shared/doorbell-bridge.log
tail -f /Users/Shared/doorbell-bridge-error.log
```

---

## SSH into Savant Host Processor

```bash
ssh RPM@<SAVANT_HOST_IP>
```

---

## Plutil – Validate LaunchDaemon Plist

```bash
plutil -lint /Library/LaunchDaemons/com.savant.doorbell.plist
```

---

## LaunchDaemon — Load / Unload / Restart

```bash
# Load a daemon:
sudo launchctl load /Library/LaunchDaemons/com.savant.doorbell-bridge.plist

# Unload a daemon:
sudo launchctl unload /Library/LaunchDaemons/com.savant.doorbell-bridge.plist

# If 'Unload failed: 5: Input/output error':
sudo launchctl bootout system /Library/LaunchDaemons/com.savant.doorbell-bridge.plist
sudo launchctl bootstrap system /Library/LaunchDaemons/com.savant.doorbell-bridge.plist

# List all loaded daemons (search for yours):
sudo launchctl list | grep savant
sudo launchctl list | grep unifi

# Validate plist syntax:
plutil -lint /Library/LaunchDaemons/com.savant.doorbell-bridge.plist
```

---

## Find & Kill Process by Name (macOS)

```bash
# Find a process:
ps aux | grep savant-doorbell-bridge | grep -v grep
ps aux | grep go2rtc | grep -v grep
ps aux | grep python3 | grep -v grep

# Kill by name:
sudo pkill -f savant-doorbell-bridge.py
sudo pkill go2rtc

# Kill by PID (from ps output):
kill -9 <PID>

# Check if port is in use:
lsof -i :8084
netstat -an | grep 8084
```

---

## go2rtc — Install on Savant Host (Apple Silicon Mac)

```bash
# Download go2rtc on your MacBook (not the Savant host — GitHub blocked on Savant LAN):
curl -L https://github.com/AlexxIT/go2rtc/releases/download/v1.9.14/go2rtc_mac_amd64.zip \
  -o ~/go2rtc.zip && unzip ~/go2rtc.zip -d ~/ && ls -lh ~/go2rtc
# Expected: ~19MB binary

# If macbook is also on blocked network, use phone hotspot then SCP:
# (Connect MacBook to hotspot, download, then reconnect to LAN)
scp ~/go2rtc rpm@<LOCAL_IP>:~/go2rtc
ssh rpm@<LOCAL_IP> "chmod +x ~/go2rtc"

# Verify on Savant host:
ssh rpm@<LOCAL_IP> "~/go2rtc -version"
# Expected: go2rtc version 1.9.14
```

---

## Savant Host — Python Environment Check

```bash
# Run on Savant host after SSHing in:
python3 --version
# Expected: Python 3.9.6

# Check for python or python2 (probably missing):
python --version 2>/dev/null || echo 'python not found'
python2 --version 2>/dev/null || echo 'python2 not found'

# Verify /Users/Shared exists:
ls /Users/Shared

# Find sclibridge:
which sclibridge 2>/dev/null
find /Users -name 'sclibridge' 2>/dev/null

# Check processor IP:
ifconfig | grep 'inet ' | grep -v 127.0.0.1

# Check running daemons:
sudo launchctl list | grep -E 'savant|unifi'
```

---

## launchctl — bootout/bootstrap (Modern macOS)

```bash
# Modern macOS (Ventura+): use bootout/bootstrap instead of unload/load

# Stop and unload a daemon:
sudo launchctl bootout system /Library/LaunchDaemons/com.savant.doorbell-bridge.plist

# Start and load a daemon:
sudo launchctl bootstrap system /Library/LaunchDaemons/com.savant.doorbell-bridge.plist

# Restart a daemon (bootout then bootstrap):
sudo launchctl bootout system /Library/LaunchDaemons/com.savant.doorbell-bridge.plist
sudo launchctl bootstrap system /Library/LaunchDaemons/com.savant.doorbell-bridge.plist

# List all loaded system daemons:
sudo launchctl list | grep savant

# Check daemon status:
sudo launchctl print system/com.savant.doorbell-bridge

# Validate plist before loading:
plutil -lint /Library/LaunchDaemons/com.savant.doorbell-bridge.plist
```

---

## go2rtc — LaunchDaemon (Auto-Start on Savant Host)

```bash
# Create LaunchDaemon for go2rtc:
sudo tee /Library/LaunchDaemons/com.savant.go2rtc.plist << 'EOF'
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>Label</key>
  <string>com.savant.go2rtc</string>
  <key>ProgramArguments</key>
  <array>
    <string>/Users/<USER>/go2rtc</string>
    <string>-config</string>
    <string>/Users/<USER>/go2rtc.yaml</string>
  </array>
  <key>RunAtLoad</key>
  <true/>
  <key>KeepAlive</key>
  <true/>
  <key>StandardOutPath</key>
  <string>/tmp/go2rtc.log</string>
  <key>StandardErrorPath</key>
  <string>/tmp/go2rtc.log</string>
</dict>
</plist>
EOF

sudo launchctl load /Library/LaunchDaemons/com.savant.go2rtc.plist

# Check running:
ps aux | grep go2rtc | grep -v grep
lsof -i :554
lsof -i :8554
```

---

## Verify All LaunchDaemons Running

```bash
# List all Savant-related daemons:
sudo launchctl list | grep -E 'savant|unifi|go2rtc|freepbx'

# Expected output (PID = running, - = stopped):
# PID  STATUS  LABEL
# 1234  0      com.savant.doorbell-bridge
# 5678  0      com.savant.go2rtc
# 9012  0      com.savant.unifi-gate-status

# Check each process:
ps aux | grep -E 'python3|go2rtc' | grep -v grep

# Check ports are open:
lsof -i :8084  # doorbell bridge
lsof -i :554   # go2rtc RTSP
lsof -i :5060  # FreePBX SIP

# View all plist files:
ls /Library/LaunchDaemons/com.savant.*

# Quick health check (all in one):
for port in 8084 554 5060; do
  lsof -i :$port > /dev/null 2>&1 && echo "Port $port: OPEN" || echo "Port $port: CLOSED"
done
```

---

## Python 3 — Quick Script Template for Savant Host

```bash
#!/usr/bin/env python3
"""
Template for Savant host automation scripts
Path: /Users/Shared/my-script.py
"""

import subprocess
import json
import urllib.request
import urllib.error

# Config
UNIFI_HOST = "https://<SAVANT_HOST_IP>:12445"
API_TOKEN = "YOUR_UNIFI_API_TOKEN"
SCLI = "/Users/Shared/Savant/Applications/<PROFILE>/sclibridge"

def run_curl(url, method="GET", data=None, headers=None):
    """Run curl via subprocess (handles self-signed certs with -k)"""
    cmd = ["curl", "-k", "-sS", "-X", method, url]
    if headers:
        for k, v in headers.items():
            cmd += ["-H", f"{k}: {v}"]
    if data:
        cmd += ["-d", json.dumps(data)]
    result = subprocess.run(cmd, capture_output=True, text=True)
    return result.stdout

def set_savant_state(variable, value):
    """Update Savant user state variable via sclibridge"""
    subprocess.run([SCLI, "writestate", f"userdefined.{variable}", str(value)])

# Example usage:
if __name__ == "__main__":
    headers = {
        "Authorization": f"Bearer {API_TOKEN}",
        "accept": "application/json",
        "content-type": "application/json"
    }
    result = run_curl(f"{UNIFI_HOST}/api/v1/developer/doors", headers=headers)
    print(result)
    set_savant_state("MainGateMode", "AUTO")
```

---
