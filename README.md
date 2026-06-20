# homelab-runbook

A collection of commands I've gathered running my own homelab and AV installs — door and gate control, Savant, networking, 3D printing, and a few automations. Everything site-specific has been pulled out and swapped for placeholders like <UNIFI_GATEWAY_IP> or <API_TOKEN>, so nothing here points at a real network. These aren't copy-paste-and-go — fill in your own values and test before running anything against live gear.

## Contents

### [UniFi/](unifi/README.md) — 34 entries

Door and gate control, the Dream Machine, cameras, and the scripts that drive them.

- [Access Doors & Gates](unifi/access-doors-gates.md) (20)
- [Dream Machine (UDM)](unifi/dream-machine.md) (8)
- [Cameras & Doorbell](unifi/cameras-doorbell.md) (3)
- [Full Scripts](unifi/scripts.md) (3)

### [Savant/](savant/README.md) — 25 entries

Host and processor work — sclibridge, LaunchDaemons, blueprints, and device integrations.

- [Host & LaunchDaemons](savant/host-launchdaemons.md) (11)
- [Video / go2rtc](savant/video-go2rtc.md) (2)
- [sclibridge & States](savant/sclibridge-states.md) (3)
- [Blueprints & XML Profiles](savant/blueprints-profiles.md) (3)
- [Device Integrations](savant/integrations.md) (5)
- [Misc](savant/misc.md) (1)

### [Network/](network/README.md) — 17 entries

Networking one-liners I keep reaching for, plus the runbook app's own commands.

- [DNS & ARP](network/dns-arp.md) (5)
- [Connectivity Tests](network/connectivity.md) (6)
- [SSH & File Transfer](network/ssh-transfer.md) (4)
- [VPN & WireGuard](network/vpn-wireguard.md) (1)
- [Runbook App](network/runbook-app.md) (1)

### [3D Printing/](printing/README.md) — 20 entries

Creality K1C and K2 Plus settings, slicer profiles, and fixes for the usual problems.

- [Temperatures](printing/temperatures.md) (5)
- [Slicer Profiles](printing/profiles.md) (8)
- [Retraction & Stringing](printing/retraction-stringing.md) (5)
- [Filament Care](printing/filament-care.md) (2)

### [Automation/](automation/README.md) — 15 entries

Home automation glue — Sonos, the doorbell bridge, the intercom, and video.

- [Doorbell & Intercom](automation/doorbell-intercom.md) (10)
- [Sonos Chimes](automation/sonos.md) (5)

---

111 entries total.

Sanitized for public sharing. Replace every placeholder with your own values before use.
