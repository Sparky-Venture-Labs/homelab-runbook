# Network — SSH & File Transfer

## SSH Key Setup (Passwordless Auth)

```bash
# Generate key (if needed)
ssh-keygen -t ed25519 -C "your-label"

# Copy key to remote host
ssh-copy-id user@<LOCAL_IP>

# Manual copy if ssh-copy-id unavailable
cat ~/.ssh/id_ed25519.pub | ssh user@<LOCAL_IP> "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"

# Test
ssh -i ~/.ssh/id_ed25519 user@<LOCAL_IP>
```

---

## SSH — Fix Host Key Mismatch Warning

```bash
# Error: WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED
# Cause: Hardware replaced at same IP, or server re-imaged

# Fix — remove old key for that IP:
ssh-keygen -R <LOCAL_IP>
ssh-keygen -R <SAVANT_HOST_IP>

# Then reconnect and type 'yes' to accept new fingerprint:
ssh RPM@<LOCAL_IP>
# Prompt: 'Are you sure you want to continue connecting (yes/no)?' → type: yes

# View all known hosts:
cat ~/.ssh/known_hosts

# Remove specific line from known_hosts manually:
nano ~/.ssh/known_hosts
# Find the line with the IP and delete it
```

---

## macOS — SCP File Transfer Between Machines

```bash
# Copy file from local Mac to remote host:
scp ~/localfile.sh RPM@<LOCAL_IP>:/Users/Shared/

# Copy file from remote host to local Mac:
scp RPM@<LOCAL_IP>:/Users/Shared/somefile.py ~/Desktop/

# Copy entire directory:
scp -r ~/myfolder/ RPM@<LOCAL_IP>:/Users/Shared/

# Copy binary (e.g., go2rtc) to Savant host:
scp ~/go2rtc rpm@<LOCAL_IP>:~/go2rtc
ssh rpm@<LOCAL_IP> "chmod +x ~/go2rtc"

# Alternative: rsync (better for large files/directories):
rsync -avz ~/scripts/ RPM@<LOCAL_IP>:/Users/Shared/
```

---

## macOS — sed In-Place Edit (BSD Syntax)

```bash
# macOS uses BSD sed — different from Linux GNU sed
# BSD sed requires '' after -i for in-place without backup:

# Replace text in file:
sed -i '' 's/old_text/new_text/g' /path/to/file

# Change port number:
sed -i '' 's/8082/8083/g' /Users/Shared/savant-doorbell-bridge.py

# Verify the change:
grep '8083' /Users/Shared/savant-doorbell-bridge.py

# With backup (saves original as file.bak):
sed -i '.bak' 's/old/new/g' file.txt

# Multiple replacements:
sed -i '' -e 's/foo/bar/g' -e 's/baz/qux/g' file.txt

# Linux GNU sed (different — no '' needed):
sed -i 's/old/new/g' file  # Linux only
```

---
