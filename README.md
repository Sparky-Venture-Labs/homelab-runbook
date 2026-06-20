# homelab-runbook

Battle-tested commands for running a homelab and a high-end AV install — UniFi Access door control, Savant, networking, 3D printing, and home automation, all in one place.

<p align="left">
  <a href="https://github.com/Sparky-Venture-Labs/homelab-runbook/blob/main/LICENSE"><img src="https://img.shields.io/github/license/Sparky-Venture-Labs/homelab-runbook" alt="License: MIT"></a>
  <a href="https://github.com/Sparky-Venture-Labs/homelab-runbook/stargazers"><img src="https://img.shields.io/github/stars/Sparky-Venture-Labs/homelab-runbook?style=social" alt="Stars"></a>
  <a href="https://github.com/Sparky-Venture-Labs/homelab-runbook/commits/main"><img src="https://img.shields.io/github/last-commit/Sparky-Venture-Labs/homelab-runbook" alt="Last commit"></a>
  <a href="https://github.com/Sparky-Venture-Labs/homelab-runbook/issues"><img src="https://img.shields.io/github/issues/Sparky-Venture-Labs/homelab-runbook" alt="Issues"></a>
</p>

These are the actual commands I reach for on my own gear, collected over years of real installs. Everything site-specific has been pulled out and replaced with placeholders like `<UNIFI_GATEWAY_IP>` and `<API_TOKEN>`, so nothing here points at a live network. **They're a starting point, not copy-paste-and-go** — drop in your own values and test before you run anything against production hardware.

## How to use this

1. Browse to the section you need below.
2. Each topic file lists commands with a short note on what they do.
3. Replace every `<PLACEHOLDER>` with your own value.
4. Test on a non-critical device first.

## What's inside

### [UniFi/](unifi/README.md) — 34 commands
Door and gate control via the UniFi Access API, the Dream Machine, cameras, and the scripts that drive them.

- [Access Doors & Gates](unifi/access-doors-gates.md) · [Dream Machine (UDM)](unifi/dream-machine.md) · [Cameras & Doorbell](unifi/cameras-doorbell.md) · [Full Scripts](unifi/scripts.md)

### [Savant/](savant/README.md) — 25 commands
Host and processor work — `sclibridge`, LaunchDaemons, blueprints, and third-party device integrations.

- [Host & LaunchDaemons](savant/host-launchdaemons.md) · [sclibridge & States](savant/sclibridge-states.md) · [Blueprints & XML Profiles](savant/blueprints-profiles.md) · [Device Integrations](savant/integrations.md) · [Video / go2rtc](savant/video-go2rtc.md)

### [Network/](network/README.md) — 17 commands
The networking one-liners I keep reaching for — DNS, ARP, connectivity checks, SSH, and VPN.

- [DNS & ARP](network/dns-arp.md) · [Connectivity Tests](network/connectivity.md) · [SSH & File Transfer](network/ssh-transfer.md) · [VPN & WireGuard](network/vpn-wireguard.md) · [Runbook App](network/runbook-app.md)

### [3D Printing/](printing/README.md) — 20 commands
Creality K1C and K2 Plus settings, OrcaSlicer profiles, and fixes for the usual print problems.

- [Temperatures](printing/temperatures.md) · [Slicer Profiles](printing/profiles.md) · [Retraction & Stringing](printing/retraction-stringing.md) · [Filament Care](printing/filament-care.md)

### [Automation/](automation/README.md) — 15 commands
Home automation glue — Sonos chimes, the doorbell bridge, the SIP intercom, and video.

- [Doorbell & Intercom](automation/doorbell-intercom.md) · [Sonos Chimes](automation/sonos.md)

## Contributing

Found a fix or have a sanitized command worth adding? Open an issue or a pull request. Keep all real IPs, tokens, hostnames, and identifiers out — use placeholders.

## License

Distributed under the MIT License. See [LICENSE](LICENSE) for details.
