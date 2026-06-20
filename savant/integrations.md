# Savant — Device Integrations

## In this file

- [EpixSky Hub — IP Control (TCP Telnet Port 4998)](#epixsky-hub-ip-control-tcp-telnet-port-4998)
- [SnapAV WB-700-IPV-12 — Outlet Power Control](#snapav-wb-700-ipv-12-outlet-power-control)
- [Bond Bridge Pro — Fireplace Integration with Savant](#bond-bridge-pro-fireplace-integration-with-savant)
- [Apple TV — IP Control via Savant](#apple-tv-ip-control-via-savant)
- [DSC TL280R — Savant RS-232 Integration](#dsc-tl280r-savant-rs-232-integration)

---

## EpixSky Hub — IP Control (TCP Telnet Port 4998)

```bash
# EpixSky Hub communicates via TCP/IP telnet on port 4998
# Send ASCII commands, postfix with \x0a (line feed)

# Test connection:
nc -v <DEVICE_IP> 4998

# Command format examples:
# Set RGB color (address 0-7, red/green/blue 000-255):
# 0red255   (set address 0, red to max)
# 1green128 (set address 1, green to half)

# Preset colors:
# 0white    0red    0blue   0green  0off

# Dynamic controls:
# 0rainbow   0strobe   0pulse

# Constellations (EpixSky specific):
# Shooting stars: 7cnr
# Twinkling:      7cnt  
# Dimming:        7cnd
# Stars on:       7con
# Stars off:      7coff

# Query state:
# ?status
```

---

## SnapAV WB-700-IPV-12 — Outlet Power Control

```bash
# Query outlet power status:
# Send: ?OutletPowerStatus=OUTLET\n
# where OUTLET is the outlet number (1-12)

# Response format:
# ?OutletPowerStatus=1,1.01,0.02,116.50\n
# Fields: outlet_index, watts, amps, voltage

# XML status message template for Savant profile:
# Parse response by comma and newline terminators
# Extract to state variables per outlet:
#   PowerWatts_1, PowerAmps_1, PowerVolts_1
```

---

## Bond Bridge Pro — Fireplace Integration with Savant

```bash
# Bond Bridge Pro required info:
# 1. LAN IP of the Bond Bridge Pro
# 2. Bond local token (Bond app → Settings → Local API Access → Token)
# 3. Device ID of the fireplace (Bond app → Device → Settings → Device ID)

# In Savant Studio/RacePoint:
# Add Bond Bridge Pro driver → enter IP + token
# Set fireplace device type = Fireplace in driver settings
# Use Device ID in Address/Device ID field

# Verify fireplace works in Bond app first before touching Savant
# If remote doesn't learn in Bond: fireplace might use 2.4 GHz RF (incompatible)
# Workaround for unsupported remotes: wire relay to fireplace dry contact loop

# Enable Trusted Tracked State in Bond app for discrete On/Off
# (instead of toggle behavior)
```

---

## Apple TV — IP Control via Savant

```bash
# Apple TV IP control requires cryptographic pairing (PIN entry)
# Modern Apple TVs (4th gen+) use Secure Remote Pairing (MRP protocol)

# Options for integration:
# 1. Ultamation module for Savant (dealer-purchased)
# 2. Home Assistant bridge with pyatv library
# 3. Homebridge with Apple TV plugin
# 4. pyatv (Python library for direct control)

# pyatv quick test (install on Mac):
pip3 install pyatv
atvremote scan
atvremote --id <device-id> pair
# Enter PIN shown on Apple TV screen
atvremote --id <device-id> playing
```

---

## DSC TL280R — Savant RS-232 Integration

```bash
# DSC TL280R communicator to Savant SSC-0012 via RS-232
# Must be R-model (TL280R) — R suffix = RS-232 support
# Standard models (TL280) do NOT have RS-232

# Serial config in Savant Profiler:
# Baud rate: check DSC documentation (typically 9600 or 19200)
# Data bits: 8
# Parity: None
# Stop bits: 1
# Flow control: None

# Wiring (DB9 to terminal block):
# DSC TX → Savant RX
# DSC RX → Savant TX
# DSC GND → Savant GND
# (Always cross TX/RX between devices)

# In Savant Profiler:
# Add DSC as serial device
# Select TL280R profile (or create custom)
# Map DSC zone events to Savant trigger states
```

---
