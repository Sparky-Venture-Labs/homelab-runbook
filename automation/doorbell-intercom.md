# Automation — Doorbell & Intercom

## In this file

- [Check Doorbell Bridge Status](#check-doorbell-bridge-status)
- [Load Savant LaunchDaemon (doorbell bridge)](#load-savant-launchdaemon-doorbell-bridge)
- [Verify Savant Doorbell Bridge Python Process](#verify-savant-doorbell-bridge-python-process)
- [LaunchDaemon — Intercom Bridge Plist](#launchdaemon-intercom-bridge-plist)
- [FreePBX (Docker) — SIP Intercom on Mac Mini](#freepbx-docker-sip-intercom-on-mac-mini)
- [Doorbell Bridge Python Script (Full)](#doorbell-bridge-python-script-full)
- [Doorbell Bridge — LaunchDaemon Install](#doorbell-bridge-launchdaemon-install)
- [FreePBX — Install via Docker on Mac Mini](#freepbx-install-via-docker-on-mac-mini)
- [SIP Intercom Architecture (UniFi UA G3 → Savant)](#sip-intercom-architecture-unifi-ua-g3-savant)
- [Savant — Check Doorbell Bridge Logs](#savant-check-doorbell-bridge-logs)

---

## Check Doorbell Bridge Status

```bash
curl http://<SAVANT_HOST_IP>:8765/status
```

---

## Load Savant LaunchDaemon (doorbell bridge)

```bash
cat > ~/com.savant.doorbell.plist << 'EOF'
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
  "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>Label</key>
  <string>com.savant.doorbell</string>
  <key>ProgramArguments</key>
  <array>
    <string>/usr/bin/python3</string>
    <string>/Users/Shared/savant-doorbell-bridge.py</string>
  </array>
  <key>RunAtLoad</key><true/>
  <key>KeepAlive</key><true/>
  <key>StandardOutPath</key>
  <string>/Users/Shared/doorbell-bridge.log</string>
  <key>StandardErrorPath</key>
  <string>/Users/Shared/doorbell-bridge-error.log</string>
  <key>WorkingDirectory</key><string>/Users/Shared</string>
</dict>
</plist>
EOF

sudo mv ~/com.savant.doorbell.plist /Library/LaunchDaemons/
sudo chmod 644 /Library/LaunchDaemons/com.savant.doorbell.plist
sudo launchctl load /Library/LaunchDaemons/com.savant.doorbell.plist
```

---

## Verify Savant Doorbell Bridge Python Process

```bash
# On Savant host via SSH:
ps aux | grep savant-doorbell-bridge
# Check port is listening:
lsof -i :8765
```

---

## LaunchDaemon — Intercom Bridge Plist

```bash
cat > ~/com.savant.unifi-intercom-bridge.plist << 'EOF'
<?xml version="1.0" encoding="UTF-8"?>
<plist version="1.0"><dict>
  <key>Label</key>
  <string>com.savant.unifi-intercom-bridge</string>
  <key>ProgramArguments</key>
  <array>
    <string>/usr/bin/python3</string>
    <string>/Users/Shared/unifi-intercom-bridge.py</string>
  </array>
  <key>RunAtLoad</key><true/>
  <key>KeepAlive</key><true/>
  <key>StandardOutPath</key>
  <string>/Users/Shared/intercom-bridge.log</string>
  <key>StandardErrorPath</key>
  <string>/Users/Shared/intercom-bridge-error.log</string>
  <key>WorkingDirectory</key><string>/Users/Shared</string>
</dict></plist>
EOF

sudo mv ~/com.savant.unifi-intercom-bridge.plist /Library/LaunchDaemons/
sudo chmod 644 /Library/LaunchDaemons/com.savant.unifi-intercom-bridge.plist
sudo launchctl load /Library/LaunchDaemons/com.savant.unifi-intercom-bridge.plist
```

---

## FreePBX (Docker) — SIP Intercom on Mac Mini

```bash
# Run FreePBX in Docker on Mac Mini
docker run -d \
  --name freepbx \
  --network host \
  --restart unless-stopped \
  tiredofit/freepbx:latest

# Check it's running
docker ps | grep freepbx
docker logs freepbx --tail 50

# Access web UI
open http://localhost/admin
```

---

## Doorbell Bridge Python Script (Full)

```bash
# Install at: /Users/Shared/savant-doorbell-bridge.py
# Port 8084 (was 8083, then 8082, then 8081 — changed due to conflicts)
# Architecture: UniFi Protect → webhook POST → Python bridge port 8084 → executes doorbell-action.sh

# Check bridge is running:
ps aux | grep savant-doorbell-bridge | grep -v grep

# Check status endpoint:
curl http://<LOCAL_IP>:8084/status

# Manually trigger (simulate doorbell press):
curl -X POST http://<LOCAL_IP>:8084/doorbell-press

# Check current state:
curl http://<LOCAL_IP>:8084/doorbell-state

# View logs:
tail -f /tmp/doorbell-bridge.log
```

---

## Doorbell Bridge — LaunchDaemon Install

```bash
# Create plist at /Library/LaunchDaemons/com.savant.doorbell-bridge.plist
sudo tee /Library/LaunchDaemons/com.savant.doorbell-bridge.plist << 'EOF'
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>Label</key>
  <string>com.savant.doorbell-bridge</string>
  <key>ProgramArguments</key>
  <array>
    <string>/usr/bin/python3</string>
    <string>/Users/Shared/savant-doorbell-bridge.py</string>
  </array>
  <key>RunAtLoad</key>
  <true/>
  <key>KeepAlive</key>
  <true/>
  <key>StandardOutPath</key>
  <string>/tmp/doorbell-bridge.log</string>
  <key>StandardErrorPath</key>
  <string>/tmp/doorbell-bridge.log</string>
</dict>
</plist>
EOF

sudo launchctl load /Library/LaunchDaemons/com.savant.doorbell-bridge.plist
sudo launchctl list | grep doorbell
```

---

## FreePBX — Install via Docker on Mac Mini

```bash
# Step 1: Install Homebrew (if not present):
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Step 2: Install Docker Desktop:
brew install --cask docker
open /Applications/Docker.app
# Wait ~30 seconds for Docker engine to start
docker --version

# Step 3: Run FreePBX container (--network host is critical for SIP):
docker run -d \
  --name freepbx \
  --restart always \
  --network host \
  -e APACHE_REDIRECT_HTTP_TO_HTTPS=false \
  tiredofit/freepbx:latest

# Step 4: Watch startup logs:
docker logs -f freepbx
# Watch for 'FreePBX is running'

# Step 5: Verify running:
docker ps | grep freepbx
```

---

## SIP Intercom Architecture (UniFi UA G3 → Savant)

```bash
# Full intercom call flow:
# UA G3 Intercom (at gate) presses doorbell
#   → Calls SIP extension via FreePBX on Mac Mini
#   → FreePBX rings Savant touch panels
#   → Touch panel shows video + audio
#   → User answers, talks, presses gate open button

# UniFi Access SIP config (GUI: Access → Interface Designer → G3 Intercom → Third-Party SIP):
# Server IP: <LOCAL_IP> (Mac Mini)
# Port: 5060
# Username: 101 (extension for that intercom)
# Password: YOUR_SIP_PASSWORD

# Savant Blueprint SIP config:
# Tools → Settings → Intercom → IP PA Systems → Add
# Each touch panel gets its own extension (201, 202, etc.)

# SIP extension plan:
# 101 → UA G3 Main Gate intercom
# 102 → UA G3 Service Gate intercom
# 201 → Savant Touch Panel (master bedroom)
# 202 → Savant Touch Panel (living room)
# 203 → Savant mobile app users
```

---

## Savant — Check Doorbell Bridge Logs

```bash
# Watch live bridge log:
tail -f /tmp/doorbell-bridge.log

# Check event history:
cat /Users/Shared/doorbell_events.log
tail -20 /Users/Shared/doorbell_events.log

# Check bridge status:
curl http://<LOCAL_IP>:8084/status

# Verify bridge is listening on correct port:
netstat -an | grep 8084
lsof -i :8084

# Test webhook manually:
curl -X POST http://<LOCAL_IP>:8084/doorbell-press
echo 'Expected: OK'

# Check current state:
curl http://<LOCAL_IP>:8084/doorbell-state
# Expected: PRESSED or IDLE
```

---
