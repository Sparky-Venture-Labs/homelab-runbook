# 3D Printing — Slicer Profiles

## In this file

- [K1C — OrcaSlicer PLA+ Profile (Direct Drive)](#k1c-orcaslicer-pla-profile-direct-drive)
- [OrcaSlicer — Pressure Advance Calibration](#orcaslicer-pressure-advance-calibration)
- [K1C — OrcaSlicer PLA+ Profile (Full Settings)](#k1c-orcaslicer-pla-profile-full-settings)
- [K1C — Z Hop Fix (High Z Hop = More Stringing)](#k1c-z-hop-fix-high-z-hop-more-stringing)
- [K1C — Bridge and Support Settings](#k1c-bridge-and-support-settings)
- [OrcaSlicer — Speed Settings Explained (K1C)](#orcaslicer-speed-settings-explained-k1c)
- [OrcaSlicer — Seam and Infill Tips](#orcaslicer-seam-and-infill-tips)
- [K1C / K2 Plus — Fan Settings (Cooling Strategy)](#k1c-k2-plus-fan-settings-cooling-strategy)

---

## K1C — OrcaSlicer PLA+ Profile (Direct Drive)

```bash
# OrcaSlicer K1C PLA+ recommended profile
Nozzle:           210-215C
Bed:              60C (first layer) / 50C (other layers)
Pressure advance: 0.04
Flow ratio:       1.00
Retraction dist:  1.0 mm
Retraction speed: 40 mm/s
Z hop:            0.2-0.3 mm (or OFF on direct drive)
Travel threshold: 2-3 mm

# Cooling
Model fan:   30-40% min / 100% max
Aux fan:     100%

# Speed
Outer wall:  100-150 mm/s
Travel:      150-200 mm/s

# Quality
Layer height:         0.20 mm
Infill:               20%
Bridge flow ratio:    0.9
Avoid crossing walls: ON
Skirt:                1-2 loops
```

---

## OrcaSlicer — Pressure Advance Calibration

```bash
# OrcaSlicer built-in PA calibration
1. OrcaSlicer -> Calibration -> Pressure Advance
2. Set PA start: 0.00, end: 0.10, step: 0.005
3. Print the pattern
4. Identify line with sharpest corners, no bulging
5. Enter that PA value in filament profile

# K1C PLA+ baseline: PA = 0.04

# Manual G-code (Klipper-based printers):
SET_PRESSURE_ADVANCE ADVANCE=0.04
```

---

## K1C — OrcaSlicer PLA+ Profile (Full Settings)

```bash
# K1C OrcaSlicer PLA+ Profile — Recommended Settings:

# === LAYER & WALLS ===
# Layer height: 0.16mm (quality) or 0.20mm (speed)
# First layer height: 0.20mm (NOT 0.16mm — better adhesion)
# Wall loops: 4 (better than default 2)
# Walls printing order: Outer/Inner (better surface)
# Avoid crossing walls: ON (reduces stringing)

# === INFILL ===
# Pattern: Gyroid (best all-around strength)
# Density: 20% (up from default 15%)
# Top layers: 5 (Monotonic Line)
# Bottom layers: 4 (Monotonic)

# === TEMPERATURES ===
# Nozzle first layer: 210°C
# Nozzle other layers: 210-215°C
# Bed first layer: 60°C
# Bed other layers: 50°C
# Chamber: 0°C (PLA needs cool chamber — no heating)

# === COOLING ===
# Fan: always on
# Min speed: 30-40% (not 100% on thick layers)
# Max speed: 100% (on thin layers, overhangs, bridges)
# Side/aux fan: 100%
# Slow down for cooling: ON, min speed 20mm/s

# === RETRACTION ===
# Length: 1.0mm (direct drive K1C)
# Speed: 40mm/s (was 46, reduced for less stress)
# Deretraction: 40mm/s
# Z hop: 0.2-0.3mm (was 0.6mm — high Z hop increases stringing)
# Travel distance threshold: 2-3mm (was 1mm — reduce unnecessary retracts)
# Retract before wipe: 70% (was 100%)

# === SPEED ===
# Outer wall: 100-150mm/s (NOT 200mm/s — biggest quality impact)
# Inner wall: 200-250mm/s
# Infill: 250-300mm/s
# First layer: 60mm/s
# Travel: 500mm/s

# === OTHER ===
# Seam: Rear (hides seam better than Aligned)
# Skirt: 1-2 loops (prime nozzle before print)
# Bridge flow: 0.90 (prevents bridge sag)
# Pressure advance: 0.04 (K1C default)
```

---

## K1C — Z Hop Fix (High Z Hop = More Stringing)

```bash
# Problem: Z hop at 0.6mm causes MORE stringing, not less
# Reason: Nozzle spends more time in air during travel = more ooze

# Fix in OrcaSlicer:
# Printer Settings → Extruder → Z Hop when retracting
# Change from 0.6mm to 0.2-0.3mm
# OR disable Z hop entirely if not seeing layer scarring

# Also check (these settings appear ORANGE = modified from default):
# Retraction speed: 46mm/s → try 40mm/s
# Travel distance threshold: 1mm → raise to 2-3mm
# Retract before wipe: 100% → lower to 70%

# Orange color in OrcaSlicer = user-modified from system default
# This is just an indicator, not necessarily wrong
```

---

## K1C — Bridge and Support Settings

```bash
# Bridge settings in OrcaSlicer:
# Bridge flow ratio: 0.90 (reduces sag — was 1.0)
# Bridge speed: 60-80mm/s (slower = better bridging)
# Force cooling for overhangs and bridges: ON
# Fan speed for bridges: 100%

# Support settings:
# Enable supports: Auto (threshold 30°)
# Remove small overhangs: ON
# Support type: Normal (Tree supports for complex geometry)
# Support interface layers: 2 (makes supports easier to remove)
# Support interface spacing: 0.2mm
# Z distance from part: 0.2mm

# Overhang threshold:
# 30°: enables supports for overhangs beyond 30° from vertical
# 45°: more permissive — most printers can do 45° without support
# For K1C with good cooling: try 40-45° threshold first
```

---

## OrcaSlicer — Speed Settings Explained (K1C)

```bash
# K1C Speed Settings — Quality vs Speed tradeoffs:

# === QUALITY-FIRST PROFILE ===
# Outer wall:        100-150mm/s  (BIGGEST quality impact — slow this down)
# Inner wall:        200-250mm/s
# Infill:            250-300mm/s
# Internal solid:    250mm/s
# Top surface:       100-150mm/s  (second biggest for finish quality)
# First layer:       60mm/s
# Travel:            500mm/s

# === BALANCED PROFILE ===
# Outer wall:        150-200mm/s
# Inner wall:        250mm/s
# Infill:            300mm/s
# Top surface:       200mm/s
# First layer:       60mm/s
# Travel:            500mm/s

# === ACCELERATION ===
# Normal:            10000 mm/s²
# Outer wall:        3000 mm/s²  (lower = less ringing on surfaces)
# Inner wall:        3000 mm/s²
# Top surface:       2000 mm/s²  (for best surface finish)

# === JERK ===
# Default:           8 mm/s
# Walls/Top:         6 mm/s
# Travel:            8 mm/s
```

---

## OrcaSlicer — Seam and Infill Tips

```bash
# SEAM POSITION:
# Aligned (default): stacks seam in one spot — visible vertical line
# Rear: hides seam on back of model — best for display pieces
# Nearest: seam at shortest travel — fastest but random placement
# Random: rotates seam around — distributes but still visible on close look
# Recommendation: Rear for quality, Random for functional parts

# SEAM WIPE:
# Wipe while retracting: ON
# Wipe distance: 2mm
# Stagger inner seams: ON
# Wipe speed: 80% (of print speed)

# INFILL PATTERN:
# Gyroid: best all-around (3D structure, strong in all directions)
# Honeycomb: classic, slightly less material efficient
# Lines/Grid: fastest to print, weakest structure
# Recommendation: Gyroid at 20% for general use

# ONLY ONE WALL ON TOP SURFACES:
# ON: faster top surface, uses inner wall for base
# This is a K1C-specific quality optimization

# REDUCE INFILL RETRACTION:
# ON: skip retraction between infill lines inside the model
# Speeds up infill, reduces wear on extruder
```

---

## K1C / K2 Plus — Fan Settings (Cooling Strategy)

```bash
# K1C / K2 Plus Fan Settings in OrcaSlicer:

# Main/Model fan:
# Keep fan always on: YES (PLA needs constant cooling)
# Min fan speed threshold: 30-40% at 100s layer time
# Max fan speed threshold: 100% at 10s layer time
# (OrcaSlicer auto-scales between these based on layer time)

# Fan speed for overhangs/bridges: 100%
# Force cooling for overhangs: ON

# Side/auxiliary fan:
# Speed: 100% (PLA — run full blast)
# This is the additional fan on K1C that helps cool prints

# Air filtration / back fan:
# Optional: enable if bothered by fumes
# Settings: 60% during print, 80% after completion
# For PLA: fumes are minimal, filtration mainly comfort

# Chamber management:
# PLA/PLA+: NO chamber heating (keep doors OPEN)
# PETG: doors slightly open or cracked
# ABS/ASA: close doors + heat chamber (not K1C's strength)

# Slow down for better cooling:
# ON, min print speed: 20mm/s
# Prevents overheating on small cross-section layers
```

---
