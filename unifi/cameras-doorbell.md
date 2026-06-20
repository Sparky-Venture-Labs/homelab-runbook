# UniFi — Cameras & Doorbell

## UniFi Webhook – Test Delivery

```bash
# Start a local listener to verify webhook payloads
nc -l 8765

# Or simulate a doorbell webhook POST to Savant bridge
curl -X POST http://<SAVANT_HOST_IP>:8765/doorbell \
  -H "Content-Type: application/json" \
  -d '{"event":"doorbell.ring","device_id":"<DEVICE_ID>"}'
```

---

## UniFi Camera Integration — Snapshot Mode (Savant)

```bash
# Savant supports UniFi cameras via snapshot JPEG (not live RTSP)
# Reason: UniFi Protect RTSP uses non-standard ports/encryption Savant can't consume

# Enable anonymous snapshots on UniFi camera:
# Camera Settings → Local IP → Enable anonymous snapshots
# Access camera's local web UI using camera's IP and recovery code

# Snapshot URL format:
# http://CAMERA_IP/snap.jpeg
# or for some models: http://CAMERA_IP:80/snap.jpeg

# In Savant Blueprint (Ubiquiti IP Camera profile):
# Add camera as 'IP Camera' or 'Ubiquiti' device type
# Set IP to camera's local IP
# Set snapshot URL path

# For live streaming in Savant:
# Use go2rtc to proxy UniFi RTSP stream
# Or use ONVIF-compatible camera instead

# Alternative: UniFi Protect snapshot URL (requires Protect API):
# https://UNIFI_IP/proxy/protect/api/cameras/CAMERA_ID/snapshot
```

---

## Savant — UniFi Camera Profile Notes

```bash
# Savant camera profile requirements (from Savant docs):
# - RTSP on port 554
# - H.264 codec
# - No custom ports, no RTSPS
# - Credentials and path go in RTSP Path field (NOT IP field)

# In Blueprint camera config:
# Profile: 'Ubiquiti' or 'IP Camera'
# IP field: camera/proxy IP only (no rtsp:// prefix)
# RTSP Path: /stream-name  (for go2rtc proxy)
# Username/Password: if auth required

# Common error: black screen in Savant
# Check 1: VLC can play the stream? rtsp://HOST:554/stream
# Check 2: H.264 enabled in go2rtc? (disable H.265 only mode)
# Check 3: Port 554 accessible? lsof -i :554 on host
# Check 4: go2rtc log shows Savant connecting?

# go2rtc log location:
tail -f /tmp/go2rtc.log
# Look for: 'INF [api] GET /api/ws' and RTSP connection from Savant IP
```

---
