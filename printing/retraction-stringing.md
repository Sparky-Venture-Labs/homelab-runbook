# 3D Printing — Retraction & Stringing

## In this file

- [Fix Stringing — Full Checklist](#fix-stringing-full-checklist)
- [Cold Pull — Nozzle Cleaning](#cold-pull-nozzle-cleaning)
- [Fix Stringing — Complete Troubleshooting Checklist](#fix-stringing-complete-troubleshooting-checklist)
- [K2 Plus — Pressure Advance Calibration](#k2-plus-pressure-advance-calibration)
- [K1C — Pressure Advance Default + Filament Settings](#k1c-pressure-advance-default-filament-settings)

---

## Fix Stringing — Full Checklist

```bash
# Stringing troubleshooting checklist (in order)

1. Dry the filament first
   PLA/PLA+: 40-45C for 4-6 hours
   Wet filament is the #1 cause of stringing

2. Lower nozzle temp 5C at a time
   Try: 210 -> 205 -> 200C

3. Increase travel speed
   Target: 150-200 mm/s

4. Enable combing / avoid crossing walls in slicer

5. Adjust retraction distance (+0.2 mm increments)

6. Disable Z hop (direct drive — Z hop worsens stringing)

7. Increase retraction speed slightly (5 mm/s steps)

8. Check for partial clog — do a cold pull
```

---

## Cold Pull — Nozzle Cleaning

```bash
# Cold pull procedure (PLA)
1. Heat nozzle to 210C
2. Push filament through manually until clean
3. Cool nozzle to 90C (not room temp)
4. Pull filament out firmly in one smooth motion
5. Inspect tip - should be a clean nozzle mold
6. Repeat 2-3x until clean

# Automated (OrcaSlicer):
Calibration -> Cold Pull

# After cold pull - test extrusion at 5mm to confirm flow
```

---

## Fix Stringing — Complete Troubleshooting Checklist

```bash
# Stringing troubleshooting order (most → least likely cause):

# 1. FILAMENT MOISTURE (most common)
#    Dry filament: 40-45°C for 4-6 hours in food dehydrator or oven
#    Store in airtight bag with desiccant after
#    Test: new spool with same settings stringing-free? = moisture issue

# 2. NOZZLE TEMPERATURE (too hot = more ooze)
#    Step down 5°C at a time (220→215→210°C)
#    Stop when surface gets rough/matte = too cold

# 3. RETRACTION SETTINGS (direct drive K1/K2)
#    Distance: 0.6-1.0mm (increase 0.1mm steps)
#    Speed: 40mm/s
#    Z hop: reduce to 0.2mm or OFF

# 4. TRAVEL SETTINGS
#    Enable Avoid Crossing Walls (combing)
#    Increase travel speed: 150-200mm/s minimum
#    Raise travel distance threshold to 2-3mm

# 5. OUTER WALL SPEED
#    Slow outer walls: 100-150mm/s
#    Faster = more ooze time over open air

# 6. FLOW RATIO
#    Set to 1.00 (start neutral)
#    Over-extrusion causes ooze = stringing

# 7. PRESSURE ADVANCE
#    K1C default: 0.04
#    Verify not accidentally changed

# QUICK TEST:
# Print retraction test tower with current settings
# Change ONE thing and retest
```

---

## K2 Plus — Pressure Advance Calibration

```bash
# K1C default PA value: 0.04
# K2 Plus PA starting value: 0.02-0.05 (calibrate per filament)

# In OrcaSlicer: Calibration → Pressure Advance → Start
# Or manually in filament profile:
# Filament Settings → Enable pressure advance: ON
# PA value: 0.04 (K1C start), adjust per calibration

# Symptoms of wrong PA:
# Too low: bulging corners, over-extrusion at corners
# Too high: gaps at corners, under-extrusion at corners

# Calibration print (PA tower):
# Print a PA calibration test
# Look for the line with sharpest corners and no gaps
# Note the value and enter in filament profile
```

---

## K1C — Pressure Advance Default + Filament Settings

```bash
# K1C default Pressure Advance: 0.04
# Verify it hasn't been accidentally changed:
# OrcaSlicer → Filament Settings → Pressure advance = 0.04

# Flow ratio:
# Start at 1.00 (neutral)
# If over-extruding (thick walls, blobby corners): 0.97-0.99
# If under-extruding (thin walls, gaps): 1.01-1.03
# Calibrate with calibration cube: measure wall thickness

# Filament profile key settings for PLA+:
# Density: 1.24-1.27 g/cm³ (PLA+ typical)
# Shrinkage: 100% (default)
# Adhesiveness: 100% (default)
# Max volumetric speed: 14 mm³/s (don't raise without testing)

# Temperature range in filament profile:
# Match what's printed on the spool!
# Example: spool says 195-220°C → set profile to 195-220°C
# Print temp: 210-215°C is safe all-around for PLA+ on K1C

# Recommended nozzle temps by speed:
# 50-100mm/s: 195-205°C
# 100-300mm/s: 205-220°C
```

---
