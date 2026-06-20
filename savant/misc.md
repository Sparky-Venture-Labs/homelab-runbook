# Savant — Misc

## Savant Camera Integration — RTSP Requirements

```bash
# Savant RTSP camera requirements:
# - Port: MUST be 554 (cannot use custom ports)
# - Codec: H.264 ONLY (H.265/HEVC not supported natively)
# - No RTSPS (encrypted RTSP not supported)
# - No custom ports (8554, 7441, etc.)

# In Blueprint camera config:
# IP field: enter camera IP only (no rtsp:// prefix)
# RTSP Path field: enter credentials + path
# Example for go2rtc proxy:
#   IP: <LOCAL_IP> (Savant host running go2rtc)
#   Path: /camera-name  (go2rtc stream name)

# Test RTSP in VLC first:
# File → Open Network Stream → rtsp://<LOCAL_IP>:554/stream-name

# UniFi camera native URL (doesn't work directly in Savant):
# rtsps://<LOCAL_IP>:7441/TOKEN?enableSrtp
```

---
