# 3D Printing — Filament Care

## Filament Degradation — Diagnosis and Recovery

```bash
# Signs of degraded/moist filament:
# - Partial print failures after a few layers
# - Popping/crackling sound during printing
# - Inconsistent extrusion (thin then thick lines)
# - Prints fail with same settings that worked on new spool
# - Light stringiness between parts

# Tests:
# Bend test: brittle and snapping easily = degraded PLA
# Visual: white powder or crystal deposits on filament
# Compare: print same file with new spool same settings

# Recovery:
# Drying: 40-45°C for 4-6 hours
# Food dehydrator works great
# Oven: lowest setting with door cracked
# DO NOT exceed 50°C for PLA — it will soften and warp

# Storage:
# Airtight bag or vacuum bag with silica gel desiccant
# Cool, dark location (away from windows)
# Replace desiccant regularly (rechargeable silica gel at 150°C)

# If drying doesn't fix it:
# Filament is mechanically degraded (UV, time, heat cycles)
# Adjust temp +5°C and speed -20%
# If still bad: discard spool
```

---

## First-Layer Adhesion Troubleshooting

```bash
# Symptoms: rounded lines that pop off, separated extruded lines,
# first layer lifts at corners, doesn't stick at all

# Priority fix order:
# 1. LOWER Z-OFFSET (nozzle too far from bed)
#    Start: lower Z by 0.05mm increments
#    Target: lines slightly squished, no gaps between lines

# 2. SLOW FIRST LAYER SPEED
#    15-25mm/s (gives filament time to bond)
#    60mm/s first layer = poor adhesion on most setups

# 3. CHECK BED TEMPERATURE
#    PLA on textured PEI: 60-65°C
#    PLA on smooth PEI: 60°C (may need higher)
#    PETG: 70-80°C

# 4. CLEAN BED SURFACE
#    IPA (isopropyl alcohol) wipe before each print
#    No fingerprints — skin oils kill adhesion
#    If still not sticking: wash plate with warm soapy water

# 5. NOZZLE TEMPERATURE (first layer)
#    Slightly higher helps: 210°C instead of 205°C

# 6. CHECK FILAMENT
#    Dry if moist (see filament degradation entry)

# 7. CHECK E-STEPS / FLOW
#    Print calibration cube, measure walls
#    Adjust flow ratio if over/under extruding
```

---
