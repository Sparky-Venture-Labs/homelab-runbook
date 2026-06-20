# UniFi — Access Doors & Gates

## In this file

- [List UniFi Access Doors](#list-unifi-access-doors)
- [Unlock Main Gate (UniFi Access)](#unlock-main-gate-unifi-access)
- [Unlock Service Gate (UniFi Access)](#unlock-service-gate-unifi-access)
- [UniFi Access API – Lock Door](#unifi-access-api-lock-door)
- [Run Gate Script Manually on Savant Host](#run-gate-script-manually-on-savant-host)
- [Gate Status Check Script](#gate-status-check-script)
- [List UniFi Access Doors (Get Door IDs)](#list-unifi-access-doors-get-door-ids)
- [Unlock Main Gate (UniFi Access API)](#unlock-main-gate-unifi-access-api)
- [Unlock Service Gate (UniFi Access API)](#unlock-service-gate-unifi-access-api)
- [Hold Gate Open (lock_rule: keep_unlock)](#hold-gate-open-lock-rule-keep-unlock)
- [Release Gate (Reset to Normal Schedule)](#release-gate-reset-to-normal-schedule)
- [UniFi Access API — Webhook Registration (Doorbell)](#unifi-access-api-webhook-registration-doorbell)
- [Gate Status Script (Poll UniFi Lock State)](#gate-status-script-poll-unifi-lock-state)
- [unifi-main-gate-open.sh — Complete Script](#unifi-main-gate-open-sh-complete-script)
- [unifi-service-gate-open.sh — Complete Script](#unifi-service-gate-open-sh-complete-script)
- [unifi-main-gate-holdopen.sh — Complete Script](#unifi-main-gate-holdopen-sh-complete-script)
- [unifi-main-gate-release.sh — Complete Script](#unifi-main-gate-release-sh-complete-script)
- [UniFi Access — Register Webhook (Doorbell Events)](#unifi-access-register-webhook-doorbell-events)
- [UniFi — Lock Door via API](#unifi-lock-door-via-api)
- [UniFi G4 Doorbell — Integration Options Summary](#unifi-g4-doorbell-integration-options-summary)

---

## List UniFi Access Doors

```bash
curl -k -X GET "https://<UNIFI_GATEWAY_IP>:12445/api/v1/developer/doors" \
  -H "Authorization: Bearer <API_TOKEN>" \
  -H "accept: application/json" \
  -H "content-type: application/json"
```

---

## Unlock Main Gate (UniFi Access)

```bash
UNIFI_HOST="https://<UNIFI_GATEWAY_IP>:12445"
DOOR_ID="<MAIN_GATE_DOOR_ID>"
API_TOKEN="<API_TOKEN>"

curl -k -sS -X PUT "${UNIFI_HOST}/api/v1/developer/doors/${DOOR_ID}/unlock" \
  -H "Authorization: Bearer ${API_TOKEN}" \
  -H "accept: application/json" \
  -H "content-type: application/json" \
  -d '{"actor_name":"Savant Main Gate"}'
```

---

## Unlock Service Gate (UniFi Access)

```bash
UNIFI_HOST="https://<UNIFI_GATEWAY_IP>:12445"
DOOR_ID="<SERVICE_GATE_DOOR_ID>"
API_TOKEN="<API_TOKEN>"

curl -k -sS -X PUT "${UNIFI_HOST}/api/v1/developer/doors/${DOOR_ID}/unlock" \
  -H "Authorization: Bearer ${API_TOKEN}" \
  -H "accept: application/json" \
  -H "content-type: application/json" \
  -d '{"actor_name":"Savant Service Gate"}'
```

---

## UniFi Access API – Lock Door

```bash
curl -k -sS -X PUT "https://<UNIFI_GATEWAY_IP>:12445/api/v1/developer/doors/<DOOR_ID>/lock" \
  -H "Authorization: Bearer <API_TOKEN>" \
  -H "accept: application/json" \
  -H "content-type: application/json" \
  -d '{"actor_name":"Manual Lock"}'
```

---

## Run Gate Script Manually on Savant Host

```bash
# After SSH into Savant host (<SAVANT_HOST_IP>):
bash /Users/Shared/unifi-main-gate-open.sh
# or
bash /Users/Shared/unifi-service-gate-open.sh
```

---

## Gate Status Check Script

```bash
#!/bin/bash
# /Users/Shared/unifi-gate-status.sh
UNIFI_HOST="https://<UNIFI_GATEWAY_IP>:12445"
API_TOKEN="<API_TOKEN>"
MAIN_GATE_ID="<MAIN_GATE_DOOR_ID>"
SERVICE_GATE_ID="<SERVICE_GATE_DOOR_ID>"

echo "=== Gate Status ==="
echo -n "Main Gate:    "
curl -k -sS -X GET "$UNIFI_HOST/api/v1/developer/doors/$MAIN_GATE_ID" \
  -H "Authorization: Bearer $API_TOKEN" \
  -H "accept: application/json" \
  | python3 -c "import sys,json; d=json.load(sys.stdin); print(d.get('lock_rule','unknown'))"

echo -n "Service Gate: "
curl -k -sS -X GET "$UNIFI_HOST/api/v1/developer/doors/$SERVICE_GATE_ID" \
  -H "Authorization: Bearer $API_TOKEN" \
  -H "accept: application/json" \
  | python3 -c "import sys,json; d=json.load(sys.stdin); print(d.get('lock_rule','unknown'))"
```

---

## List UniFi Access Doors (Get Door IDs)

```bash
curl -k -X GET "https://<SAVANT_HOST_IP>:12445/api/v1/developer/doors" \
  -H "Authorization: Bearer REDACTED_ROTATED_TOKEN" \
  -H "accept: application/json" \
  -H "content-type: application/json"
```

---

## Unlock Main Gate (UniFi Access API)

```bash
curl -k -sS -X PUT "https://<SAVANT_HOST_IP>:12445/api/v1/developer/doors/<DOOR_ID>/unlock" \
  -H "Authorization: Bearer REDACTED_ROTATED_TOKEN" \
  -H "accept: application/json" \
  -H "content-type: application/json" \
  -d '{"actor_name": "Savant Main Gate"}'
```

---

## Unlock Service Gate (UniFi Access API)

```bash
curl -k -sS -X PUT "https://<SAVANT_HOST_IP>:12445/api/v1/developer/doors/<DOOR_ID>/unlock" \
  -H "Authorization: Bearer REDACTED_ROTATED_TOKEN" \
  -H "accept: application/json" \
  -H "content-type: application/json" \
  -d '{"actor_name": "Savant Service Gate"}'
```

---

## Hold Gate Open (lock_rule: keep_unlock)

```bash
# Hold Main Gate open indefinitely:
curl -k -sS -X PUT "https://<SAVANT_HOST_IP>:12445/api/v1/developer/doors/<DOOR_ID>/lock_rule" \
  -H "Authorization: Bearer REDACTED_ROTATED_TOKEN" \
  -H "accept: application/json" \
  -H "content-type: application/json" \
  -d '{"type":"keep_unlock"}'

# Hold Service Gate open indefinitely:
curl -k -sS -X PUT "https://<SAVANT_HOST_IP>:12445/api/v1/developer/doors/<DOOR_ID>/lock_rule" \
  -H "Authorization: Bearer REDACTED_ROTATED_TOKEN" \
  -H "accept: application/json" \
  -H "content-type: application/json" \
  -d '{"type":"keep_unlock"}'
```

---

## Release Gate (Reset to Normal Schedule)

```bash
# Reset Main Gate to normal schedule:
curl -k -sS -X PUT "https://<SAVANT_HOST_IP>:12445/api/v1/developer/doors/<DOOR_ID>/lock_rule" \
  -H "Authorization: Bearer REDACTED_ROTATED_TOKEN" \
  -H "accept: application/json" \
  -H "content-type: application/json" \
  -d '{"type":"reset"}'

# Reset Service Gate to normal schedule:
curl -k -sS -X PUT "https://<SAVANT_HOST_IP>:12445/api/v1/developer/doors/<DOOR_ID>/lock_rule" \
  -H "Authorization: Bearer REDACTED_ROTATED_TOKEN" \
  -H "accept: application/json" \
  -H "content-type: application/json" \
  -d '{"type":"reset"}'
```

---

## UniFi Access API — Webhook Registration (Doorbell)

```bash
# Register webhook for doorbell/intercom press events:
curl -k -sS -X POST "https://<SAVANT_HOST_IP>:12445/api/v1/developer/webhooks" \
  -H "Authorization: Bearer REDACTED_ROTATED_TOKEN" \
  -H "accept: application/json" \
  -H "content-type: application/json" \
  -d '{
    "url": "http://<LOCAL_IP>:8084/doorbell-press",
    "events": ["access.doorbell.incoming"]
  }'

# List existing webhooks:
curl -k -sS -X GET "https://<SAVANT_HOST_IP>:12445/api/v1/developer/webhooks" \
  -H "Authorization: Bearer REDACTED_ROTATED_TOKEN"
```

---

## Gate Status Script (Poll UniFi Lock State)

```bash
# /Users/Shared/unifi-gate-status.sh
#!/bin/bash

UNIFI_HOST="https://<SAVANT_HOST_IP>:12445"
API_TOKEN="REDACTED_ROTATED_TOKEN"
MAIN_ID="<DOOR_ID>"
SERVICE_ID="<DOOR_ID>"

function door_status() {
  local name="$1"
  local id="$2"
  json=$(curl -k -sS -X GET "${UNIFI_HOST}/api/v1/developer/doors/${id}" \
    -H "Authorization: Bearer ${API_TOKEN}" \
    -H "accept: application/json" \
    -H "content-type: application/json")
  lock_state=$(echo "$json" | python3 -c "import sys, json; d=json.load(sys.stdin); print(d['data']['door_lock_relay_status'])")
  pos_state=$(echo "$json" | python3 -c "import sys, json; d=json.load(sys.stdin); print(d['data']['door_position_status'])")
  echo "${name}:${lock_state}:${pos_state}"
}

door_status "MAIN_GATE" "$MAIN_ID"
door_status "SERVICE_GATE" "$SERVICE_ID"
```

---

## unifi-main-gate-open.sh — Complete Script

```bash
# Create script on Savant host:
cat > /Users/Shared/unifi-main-gate-open.sh << 'EOF'
#!/bin/bash
UNIFI_HOST="https://<SAVANT_HOST_IP>:12445"
API_TOKEN="REDACTED_ROTATED_TOKEN"
DOOR_ID="<DOOR_ID>"
curl -k -sS -X PUT "${UNIFI_HOST}/api/v1/developer/doors/${DOOR_ID}/unlock" \
  -H "Authorization: Bearer ${API_TOKEN}" \
  -H "accept: application/json" \
  -H "content-type: application/json" \
  -d '{"actor_name": "Savant Main Gate"}'
EOF
chmod +x /Users/Shared/unifi-main-gate-open.sh

# Test:
/Users/Shared/unifi-main-gate-open.sh
# Expected: {"code":"SUCCESS","data":"success","msg":"success"}
```

---

## unifi-service-gate-open.sh — Complete Script

```bash
cat > /Users/Shared/unifi-service-gate-open.sh << 'EOF'
#!/bin/bash
UNIFI_HOST="https://<SAVANT_HOST_IP>:12445"
API_TOKEN="REDACTED_ROTATED_TOKEN"
DOOR_ID="<DOOR_ID>"
curl -k -sS -X PUT "${UNIFI_HOST}/api/v1/developer/doors/${DOOR_ID}/unlock" \
  -H "Authorization: Bearer ${API_TOKEN}" \
  -H "accept: application/json" \
  -H "content-type: application/json" \
  -d '{"actor_name": "Savant Service Gate"}'
EOF
chmod +x /Users/Shared/unifi-service-gate-open.sh

/Users/Shared/unifi-service-gate-open.sh
```

---

## unifi-main-gate-holdopen.sh — Complete Script

```bash
cat > /Users/Shared/unifi-main-gate-holdopen.sh << 'EOF'
#!/bin/bash
UNIFI_HOST="https://<SAVANT_HOST_IP>:12445"
API_TOKEN="REDACTED_ROTATED_TOKEN"
DOOR_ID="<DOOR_ID>"
SCLIBRIDGE="/Users/Shared/Savant/Applications/<PROFILE>/sclibridge"
curl -k -sS -X PUT "${UNIFI_HOST}/api/v1/developer/doors/${DOOR_ID}/lock_rule" \
  -H "Authorization: Bearer ${API_TOKEN}" \
  -H "accept: application/json" \
  -H "content-type: application/json" \
  -d '{"type":"keep_unlock"}'
$SCLIBRIDGE writestate 'userdefined.MainGateMode' 'HOLD_OPEN'
EOF
chmod +x /Users/Shared/unifi-main-gate-holdopen.sh
```

---

## unifi-main-gate-release.sh — Complete Script

```bash
cat > /Users/Shared/unifi-main-gate-release.sh << 'EOF'
#!/bin/bash
UNIFI_HOST="https://<SAVANT_HOST_IP>:12445"
API_TOKEN="REDACTED_ROTATED_TOKEN"
DOOR_ID="<DOOR_ID>"
SCLIBRIDGE="/Users/Shared/Savant/Applications/<PROFILE>/sclibridge"
curl -k -sS -X PUT "${UNIFI_HOST}/api/v1/developer/doors/${DOOR_ID}/lock_rule" \
  -H "Authorization: Bearer ${API_TOKEN}" \
  -H "accept: application/json" \
  -H "content-type: application/json" \
  -d '{"type":"reset"}'
$SCLIBRIDGE writestate 'userdefined.MainGateMode' 'AUTO'
EOF
chmod +x /Users/Shared/unifi-main-gate-release.sh
```

---

## UniFi Access — Register Webhook (Doorbell Events)

```bash
# Register webhook for all doorbell/intercom press events:
curl -k -sS -X POST "https://<SAVANT_HOST_IP>:12445/api/v1/developer/webhooks" \
  -H "Authorization: Bearer REDACTED_ROTATED_TOKEN" \
  -H "accept: application/json" \
  -H "content-type: application/json" \
  -d '{
    "url": "http://<LOCAL_IP>:8084/doorbell-press",
    "events": ["access.doorbell.incoming"]
  }'

# List registered webhooks:
curl -k -sS -X GET "https://<SAVANT_HOST_IP>:12445/api/v1/developer/webhooks" \
  -H "Authorization: Bearer REDACTED_ROTATED_TOKEN"

# Delete a webhook:
curl -k -sS -X DELETE "https://<SAVANT_HOST_IP>:12445/api/v1/developer/webhooks/WEBHOOK_ID" \
  -H "Authorization: Bearer REDACTED_ROTATED_TOKEN"
```

---

## UniFi — Lock Door via API

```bash
# Force-lock Main Gate (keep_lock):
curl -k -sS -X PUT "https://<SAVANT_HOST_IP>:12445/api/v1/developer/doors/<DOOR_ID>/lock_rule" \
  -H "Authorization: Bearer REDACTED_ROTATED_TOKEN" \
  -H "accept: application/json" \
  -H "content-type: application/json" \
  -d '{"type":"keep_lock"}'

# Force-lock Service Gate:
curl -k -sS -X PUT "https://<SAVANT_HOST_IP>:12445/api/v1/developer/doors/<DOOR_ID>/lock_rule" \
  -H "Authorization: Bearer REDACTED_ROTATED_TOKEN" \
  -H "accept: application/json" \
  -H "content-type: application/json" \
  -d '{"type":"keep_lock"}'

# Timed unlock (15 minutes then auto-lock):
curl -k -sS -X PUT "https://<SAVANT_HOST_IP>:12445/api/v1/developer/doors/<DOOR_ID>/lock_rule" \
  -H "Authorization: Bearer REDACTED_ROTATED_TOKEN" \
  -H "accept: application/json" \
  -H "content-type: application/json" \
  -d '{"type":"custom","interval":15}'
```

---

## UniFi G4 Doorbell — Integration Options Summary

```bash
# UniFi G4 Doorbell integration paths:

# Option 1: Savant via Python Bridge (IMPLEMENTED)
# G4 press → UniFi webhook → Python bridge port 8084
# → doorbell-action.sh → Sonos chime on 3 speakers
# Savant triggers: any actions wired to doorbell state variable

# Option 2: Savant GPIO dry-contact
# Use automation platform to watch G4 event
# Energize relay → dry contact on Savant GPIO input
# Savant sees contact change → AISDoorbell trigger

# Option 3: Savant SIP Intercom (IN PROGRESS)
# UA G3 Intercom (at gate) dials SIP extension
# FreePBX on Mac Mini routes call to Savant touch panels
# Full two-way video+audio intercom to touch panels

# Option 4: Control4 (NOT in use)
# Third-party C4 driver (CinDev/DriverCentral) for G4 Doorbell
# Full Protect integration: events, video, intercom
# Not available for Savant platform

# G4 Doorbell limitations:
# - No native two-way intercom via SIP (G4 Doorbell, not G3 Intercom)
# - UA G3 Intercom DOES support SIP (used at gates)
# - G4 can provide event webhooks and camera streams only
```

---
