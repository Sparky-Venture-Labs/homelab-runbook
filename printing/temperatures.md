# 3D Printing — Temperatures

## In this file

- [K2 Plus / K1C — PLA Temperature Baseline](#k2-plus-k1c-pla-temperature-baseline)
- [Retraction Tuning — Per-Filament Baselines](#retraction-tuning-per-filament-baselines)
- [K2 Plus — PLA Temperature Baseline](#k2-plus-pla-temperature-baseline)
- [Retraction Tuning — Per Filament Baselines](#retraction-tuning-per-filament-baselines)
- [OrcaSlicer — Bed Temperature (Textured vs Smooth PEI)](#orcaslicer-bed-temperature-textured-vs-smooth-pei)

---

## K2 Plus / K1C — PLA Temperature Baseline

```bash
# Creality K2 Plus / K1C — PLA starting temps
Nozzle:   190-230C  (start at 210C, drop 5C to reduce stringing)
Bed:      55-60C    (first layer), 50C (remaining layers)

# If stringing persists - reduce nozzle in 5C steps:
210 -> 205 -> 200 -> 195C
# Do NOT go below 185C for standard PLA
```

---

## Retraction Tuning — Per-Filament Baselines

```bash
# Creality K2 Plus / K1C retraction baselines (direct drive)

## PLA
Retraction distance: 0.6-0.8 mm
Retraction speed:    40-50 mm/s

## PLA+
Retraction distance: 1.0 mm
Retraction speed:    40 mm/s

## PETG
Retraction distance: 0.8-1.0 mm
Retraction speed:    30-40 mm/s

# Tuning order (change ONE at a time):
1. Adjust distance first (0.2 mm steps)
2. Then adjust speed (5 mm/s steps)
3. Then lower nozzle temp 5C
4. Enable combing / avoid crossing walls
```

---

## K2 Plus — PLA Temperature Baseline

```bash
# Creality K2 Plus PLA settings on textured PEI:
# Nozzle: 200-205°C (start here, adjust per spool)
# Bed: 60-65°C first layer, 50-55°C after

# For PLA+ on K2 Plus:
# Nozzle: 210-215°C (stiffer material needs more heat)
# Bed: 60°C first layer, 50°C after

# Retraction (DIRECT DRIVE — much less than Bowden):
# PLA:  0.6-0.8mm @ 40-50mm/s
# PLA+: 0.8-1.0mm @ 40-50mm/s
# PETG: 0.8-1.0mm @ 30-40mm/s
# DO NOT exceed 1.0-1.2mm on direct drive or you'll get jams

# K2 Plus vs K1C note:
# K2 Plus: multi-material capable, larger format
# K1C: single filament, faster CoreXY speed, similar direct drive
```

---

## Retraction Tuning — Per Filament Baselines

```bash
# Direct-drive retraction baselines (K2 Plus / K1C):

# PLA / PLA+:
#   Distance: 0.6-1.0mm (start at 0.6, increase 0.1mm steps)
#   Speed:    40-50mm/s (40mm/s is safest)
#   Max:      1.0-1.2mm (never exceed — causes jams)

# PETG:
#   Distance: 0.8-1.0mm
#   Speed:    30-40mm/s

# TPU/Flexible:
#   Distance: 0.5-0.8mm
#   Speed:    20-30mm/s

# Z Hop:
#   Recommended: 0.2-0.3mm (or OFF)
#   High Z hop (0.6mm) INCREASES stringing on K1C

# Wipe distance: 2mm (keeps nozzle clean at end of move)
# Travel threshold: 2-3mm (skip retract on very short moves)

# Tuning method:
# 1. Print retraction tower test
# 2. Start at minimum distance
# 3. Change ONE setting at a time
# 4. If extruder clicks/grinds: too much retraction
# 5. If still stringing: increase distance by 0.1mm, NOT speed
```

---

## OrcaSlicer — Bed Temperature (Textured vs Smooth PEI)

```bash
# Textured PEI plate:
# PLA/PLA+: 60°C first layer, 50°C other layers
# (Can go 65°C first layer for stubborn filaments)

# Smooth PEI plate:
# PLA/PLA+: 60°C first layer, 50°C other layers
# May need light glue stick for small contact area parts
# Higher temps (65°C) if parts are lifting at edges

# PETG:
# Both plates: 70-80°C first layer, 65°C other layers
# PETG bonds TOO well to some surfaces — use separator (glue stick)

# Can use same temps for both plate types:
# 60°C first / 50°C other works for both as baseline

# Adjustment signs:
# Parts won't release from textured PEI: lower to 55°C first layer
# Parts lifting on smooth PEI: raise to 65°C or add glue

# In OrcaSlicer filament settings:
# Bed temperature (smooth PEI): 60
# Bed temperature (textured PEI): 60
# Both can be same value
```

---
