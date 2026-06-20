# Automation — Sonos Chimes

## In this file

- [Sonos — Trigger Doorbell Chime via curl](#sonos-trigger-doorbell-chime-via-curl)
- [Doorbell Action Script (Sonos Chime — 3 Speakers)](#doorbell-action-script-sonos-chime-3-speakers)
- [Sonos — Raw SOAP Commands via curl](#sonos-raw-soap-commands-via-curl)
- [Sonos — Troubleshoot: Late Start or Cut Off](#sonos-troubleshoot-late-start-or-cut-off)
- [Sonos — LaunchDaemon for Gate Status Monitor](#sonos-launchdaemon-for-gate-status-monitor)

---

## Sonos — Trigger Doorbell Chime via curl

```bash
SONOS_IP="<DEVICE_IP>"
CLIP_URL="http://<SAVANT_HOST_IP>:8765/chime.mp3"

# Step 1: Set transport URI
curl -X POST "http://SONOS_IP:1400/MediaRenderer/AVTransport/Control" \
  -H 'Content-Type: text/xml; charset="utf-8"' \
  -H 'SOAPAction: "urn:schemas-upnp-org:service:AVTransport:1#SetAVTransportURI"' \
  -d '<?xml version="1.0"?>
<s:Envelope xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
  <s:Body>
    <u:SetAVTransportURI xmlns:u="urn:schemas-upnp-org:service:AVTransport:1">
      <InstanceID>0</InstanceID>
      <CurrentURI>CLIP_URL_HERE</CurrentURI>
      <CurrentURIMetaData></CurrentURIMetaData>
    </u:SetAVTransportURI>
  </s:Body>
</s:Envelope>'

# Step 2: Play
curl -X POST "http://SONOS_IP:1400/MediaRenderer/AVTransport/Control" \
  -H 'Content-Type: text/xml; charset="utf-8"' \
  -H 'SOAPAction: "urn:schemas-upnp-org:service:AVTransport:1#Play"' \
  -d '<?xml version="1.0"?>
<s:Envelope xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
  <s:Body>
    <u:Play xmlns:u="urn:schemas-upnp-org:service:AVTransport:1">
      <InstanceID>0</InstanceID><Speed>1</Speed>
    </u:Play>
  </s:Body>
</s:Envelope>'
```

---

## Doorbell Action Script (Sonos Chime — 3 Speakers)

```bash
cat > /Users/Shared/doorbell-action.sh << 'EOF'
#!/bin/bash
echo "$(date): Doorbell pressed" >> /Users/Shared/doorbell_events.log
SONOS1="<LOCAL_IP>"
SONOS2="<LOCAL_IP>"
SONOS3="<LOCAL_IP>"
CHIMEDURATION=6

get_volume() {
  local IP=$1
  curl -s -X POST "http://${IP}:1400/MediaRenderer/RenderingControl/Control" \
    -H "SOAPACTION: urn:schemas-upnp-org:service:RenderingControl:1#GetVolume" \
    -d '<s:Envelope xmlns:s="http://schemas.xmlsoap.org/soap/envelope/"><s:Body><u:GetVolume xmlns:u="urn:schemas-upnp-org:service:RenderingControl:1"><InstanceID>0</InstanceID><Channel>Master</Channel></u:GetVolume></s:Body></s:Envelope>' \
    | grep -o '<CurrentVolume>[0-9]*</CurrentVolume>' | grep -o '[0-9]*'
}

set_volume() {
  local IP=$1; local VOL=$2
  curl -s -X POST "http://${IP}:1400/MediaRenderer/RenderingControl/Control" \
    -H "SOAPACTION: urn:schemas-upnp-org:service:RenderingControl:1#SetVolume" \
    -d "<s:Envelope xmlns:s='http://schemas.xmlsoap.org/soap/envelope/'><s:Body><u:SetVolume xmlns:u='urn:schemas-upnp-org:service:RenderingControl:1'><InstanceID>0</InstanceID><Channel>Master</Channel><DesiredVolume>${VOL}</DesiredVolume></u:SetVolume></s:Body></s:Envelope>" > /dev/null 2>&1
}

play_chime() {
  local IP=$1
  curl -s -X POST "http://${IP}:1400/MediaRenderer/AVTransport/Control" \
    -H "SOAPACTION: urn:schemas-upnp-org:service:AVTransport:1#SetAVTransportURI" \
    -d '<s:Envelope xmlns:s="http://schemas.xmlsoap.org/soap/envelope/"><s:Body><u:SetAVTransportURI xmlns:u="urn:schemas-upnp-org:service:AVTransport:1"><InstanceID>0</InstanceID><CurrentURI>x-rincon-buzzer:0</CurrentURI><CurrentURIMetaData></CurrentURIMetaData></u:SetAVTransportURI></s:Body></s:Envelope>' > /dev/null 2>&1
  curl -s -X POST "http://${IP}:1400/MediaRenderer/AVTransport/Control" \
    -H "SOAPACTION: urn:schemas-upnp-org:service:AVTransport:1#Play" \
    -d '<s:Envelope xmlns:s="http://schemas.xmlsoap.org/soap/envelope/"><s:Body><u:Play xmlns:u="urn:schemas-upnp-org:service:AVTransport:1"><InstanceID>0</InstanceID><Speed>1</Speed></u:Play></s:Body></s:Envelope>' > /dev/null 2>&1
}

stop_playback() {
  local IP=$1
  curl -s -X POST "http://${IP}:1400/MediaRenderer/AVTransport/Control" \
    -H "SOAPACTION: urn:schemas-upnp-org:service:AVTransport:1#Stop" \
    -d '<s:Envelope xmlns:s="http://schemas.xmlsoap.org/soap/envelope/"><s:Body><u:Stop xmlns:u="urn:schemas-upnp-org:service:AVTransport:1"><InstanceID>0</InstanceID></u:Stop></s:Body></s:Envelope>' > /dev/null 2>&1
}

VOL1=$(get_volume $SONOS1); VOL2=$(get_volume $SONOS2); VOL3=$(get_volume $SONOS3)
set_volume $SONOS1 40; set_volume $SONOS2 40; set_volume $SONOS3 40
play_chime $SONOS1 & play_chime $SONOS2 & play_chime $SONOS3 &
sleep $CHIMEDURATION
stop_playback $SONOS1 & stop_playback $SONOS2 & stop_playback $SONOS3 &
sleep 1
set_volume $SONOS1 $VOL1; set_volume $SONOS2 $VOL2; set_volume $SONOS3 $VOL3
echo "$(date): Chime complete" >> /Users/Shared/doorbell_events.log
EOF
chmod +x /Users/Shared/doorbell-action.sh
```

---

## Sonos — Raw SOAP Commands via curl

```bash
# Test Sonos is reachable:
curl -I http://<LOCAL_IP>:1400/
# Expected: 403 Forbidden (this is NORMAL — Sonos IS online)

# Get current volume:
curl -s -X POST "http://<LOCAL_IP>:1400/MediaRenderer/RenderingControl/Control" \
  -H "SOAPACTION: urn:schemas-upnp-org:service:RenderingControl:1#GetVolume" \
  -d '<s:Envelope xmlns:s="http://schemas.xmlsoap.org/soap/envelope/"><s:Body><u:GetVolume xmlns:u="urn:schemas-upnp-org:service:RenderingControl:1"><InstanceID>0</InstanceID><Channel>Master</Channel></u:GetVolume></s:Body></s:Envelope>' \
  | grep -o '<CurrentVolume>[0-9]*</CurrentVolume>'

# Set volume to 40:
curl -s -X POST "http://<LOCAL_IP>:1400/MediaRenderer/RenderingControl/Control" \
  -H "SOAPACTION: urn:schemas-upnp-org:service:RenderingControl:1#SetVolume" \
  -d '<s:Envelope xmlns:s="http://schemas.xmlsoap.org/soap/envelope/"><s:Body><u:SetVolume xmlns:u="urn:schemas-upnp-org:service:RenderingControl:1"><InstanceID>0</InstanceID><Channel>Master</Channel><DesiredVolume>40</DesiredVolume></u:SetVolume></s:Body></s:Envelope>'

# Load audio and play:
curl -s -X POST "http://<LOCAL_IP>:1400/MediaRenderer/AVTransport/Control" \
  -H "SOAPACTION: urn:schemas-upnp-org:service:AVTransport:1#SetAVTransportURI" \
  -d '<s:Envelope xmlns:s="http://schemas.xmlsoap.org/soap/envelope/"><s:Body><u:SetAVTransportURI xmlns:u="urn:schemas-upnp-org:service:AVTransport:1"><InstanceID>0</InstanceID><CurrentURI>x-rincon-buzzer:0</CurrentURI><CurrentURIMetaData></CurrentURIMetaData></u:SetAVTransportURI></s:Body></s:Envelope>'

curl -s -X POST "http://<LOCAL_IP>:1400/MediaRenderer/AVTransport/Control" \
  -H "SOAPACTION: urn:schemas-upnp-org:service:AVTransport:1#Play" \
  -d '<s:Envelope xmlns:s="http://schemas.xmlsoap.org/soap/envelope/"><s:Body><u:Play xmlns:u="urn:schemas-upnp-org:service:AVTransport:1"><InstanceID>0</InstanceID><Speed>1</Speed></u:Play></s:Body></s:Envelope>'

# Stop playback:
curl -s -X POST "http://<LOCAL_IP>:1400/MediaRenderer/AVTransport/Control" \
  -H "SOAPACTION: urn:schemas-upnp-org:service:AVTransport:1#Stop" \
  -d '<s:Envelope xmlns:s="http://schemas.xmlsoap.org/soap/envelope/"><s:Body><u:Stop xmlns:u="urn:schemas-upnp-org:service:AVTransport:1"><InstanceID>0</InstanceID></u:Stop></s:Body></s:Envelope>'
```

---

## Sonos — Troubleshoot: Late Start or Cut Off

```bash
# Problem: Sonos chime plays but starts late or gets cut off
# Cause: Sonos was in sleep/idle state and takes time to wake

# Fix 1: Increase chime duration
CHIMEDURATION=10  # was 6-7, try 10-12 seconds

# Fix 2: Add pause after Play command before sleep timer:
# play_chime calls...
sleep 0.5  # allow Sonos to start playback
sleep $CHIMEDURATION
# ...stop calls

# Fix 3: Pre-wake Sonos with lightweight call at script start:
curl -s "http://<LOCAL_IP>:1400/status/topology" > /dev/null
curl -s "http://<LOCAL_IP>:1400/status/topology" > /dev/null
curl -s "http://<LOCAL_IP>:1400/status/topology" > /dev/null
sleep 1  # give speakers 1 second to wake
# then proceed with volume/play

# Fix 4: Test with single Sonos speaker to isolate latency:
# Comment out SONOS2 and SONOS3 calls, test with just SONOS1
```

---

## Sonos — LaunchDaemon for Gate Status Monitor

```bash
# LaunchDaemon for gate status polling:
sudo tee /Library/LaunchDaemons/com.savant.unifi-gate-status.plist << 'EOF'
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>Label</key>
  <string>com.savant.unifi-gate-status</string>
  <key>ProgramArguments</key>
  <array>
    <string>/bin/bash</string>
    <string>/Users/Shared/unifi-gate-status.sh</string>
  </array>
  <key>StartInterval</key>
  <integer>5</integer>
  <key>RunAtLoad</key>
  <true/>
  <key>StandardOutPath</key>
  <string>/tmp/gate-status.log</string>
  <key>StandardErrorPath</key>
  <string>/tmp/gate-status.log</string>
</dict>
</plist>
EOF

sudo launchctl load /Library/LaunchDaemons/com.savant.unifi-gate-status.plist
```

---
