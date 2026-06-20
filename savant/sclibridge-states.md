# Savant — sclibridge & States

## sclibridge — Write Savant User State

```bash
# Find sclibridge path:
which sclibridge
find / -name "sclibridge" 2>/dev/null | head

# Typical path on new processor:
/Users/Shared/Savant/Applications/<PROFILE>/sclibridge

# Write a user state:
/Users/Shared/Savant/Applications/<PROFILE>/sclibridge writestate 'userdefined.MainGateMode' 'HOLD_OPEN'
/Users/Shared/Savant/Applications/<PROFILE>/sclibridge writestate 'userdefined.MainGateMode' 'AUTO'
/Users/Shared/Savant/Applications/<PROFILE>/sclibridge writestate 'userdefined.ServiceGateMode' 'HOLD_OPEN'
/Users/Shared/Savant/Applications/<PROFILE>/sclibridge writestate 'userdefined.ServiceGateMode' 'AUTO'
```

---

## Savant — User State Variables (sclibridge)

```bash
# In Blueprint: Tools → Review → User State Variables → Create String variable
# Name examples:
#   userdefined.MainGateMode      (values: AUTO, HOLD_OPEN, OPENING, LOCKED)
#   userdefined.ServiceGateMode   (values: AUTO, HOLD_OPEN)
#   userdefined.DoorbellState     (values: IDLE, PRESSED)

# Write from script:
SCLI="/Users/Shared/Savant/Applications/<PROFILE>/sclibridge"
$SCLI writestate 'userdefined.MainGateMode' 'OPENING'
sleep 5
$SCLI writestate 'userdefined.MainGateMode' 'AUTO'

# Toggle button (1/0 or true/false only):
$SCLI writestate 'userdefined.GateHeld' '1'
$SCLI writestate 'userdefined.GateHeld' '0'
```

---

## Savant XML Profile — State Mapping Explained

```bash
<!-- State mapping translates raw sensor signals to human-readable states -->
<!-- Example: GarageDoorStatus maps High/Low to Open/Closed -->

<!-- In your XML profile, state_map section: -->
<!-- Trigger value 'High' → display 'Open' -->
<!-- Trigger value 'Low'  → display 'Closed' -->

<!-- In Blueprint Triggers: -->
<!-- When userdefined.GarageDoorStatus changes to 'Open' → trigger action -->
<!-- When userdefined.GarageDoorStatus changes to 'Closed' → trigger other action -->
```

---
