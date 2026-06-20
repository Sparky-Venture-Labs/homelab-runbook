# Savant — Blueprints & XML Profiles

## Savant Blueprint — Wire Script to Workflow

```bash
# In RacePoint Blueprint:
# 1. Create a Custom Service (or use existing Host Commands)
# 2. Add a Workflow
# 3. In the workflow Actions, add 'Execute Shell Command'
# 4. Command line: /Users/Shared/unifi-main-gate-open.sh
# 5. Upload configuration to processor

# Test from command line:
/Users/Shared/unifi-main-gate-open.sh

# All scripts directory:
ls -la /Users/Shared/*.sh
ls -la /Users/Shared/*.py
```

---

## Savant XML Profile — Troubleshooting Cannot Open

```bash
# Validate XML syntax (on Mac):
xmllint --noout your-profile.xml

# Check line endings (must be Unix LF, not Windows CRLF):
file your-profile.xml
# Good: 'XML 1.0 document, ASCII text'
# Bad:  '...with CRLF line terminators'

# Fix line endings:
sed -i '' 's/\r//' your-profile.xml
# or open in TextEdit, Save As → LF

# Save file as: plain .xml, UTF-8 encoding, Unix line endings (LF)
# In TextEdit: Format → Make Plain Text, then save with .xml extension
# In VS Code: bottom-right corner shows 'CRLF' — click to change to LF
```

---

## Savant — Verify Script Runs from Blueprint

```bash
# Before wiring into Blueprint, always test manually:
/Users/Shared/unifi-main-gate-open.sh
# Expected: {"code":"SUCCESS","data":"success","msg":"success"}

/Users/Shared/doorbell-action.sh
# Expected: Sonos speakers play chime, log entry in doorbell_events.log

# If script fails silently in Blueprint:
# 1. Check script is executable:
ls -la /Users/Shared/*.sh
# Must show: -rwxr-xr-x (the x bits = executable)

# 2. Run manually on host to see errors:
bash -x /Users/Shared/unifi-main-gate-open.sh
# -x flag prints each command as it executes

# 3. Check for path issues:
# Blueprint runs scripts with minimal PATH
# Use full paths in scripts (e.g., /usr/bin/curl not just curl)
which curl
which python3

# 4. Savant logs (if available):
# Check Savant application logs in RacePoint/Blueprint
# Filter for script execution errors
```

---
