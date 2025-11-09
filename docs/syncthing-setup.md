# Syncthing Setup and Configuration Guide

This guide provides detailed instructions for configuring Syncthing across all devices in your snippet synchronization system.

## Overview

Syncthing is a continuous file synchronization program that synchronizes files between two or more computers in real-time. For this system, it keeps the Espanso configuration folder synchronized across Windows, macOS, and Raspberry Pi.

## Core Concepts

### Devices
Each computer running Syncthing is called a **device**. Every device has a unique **Device ID**.

### Folders
**Folders** are directories that you want to synchronize between devices. For this system, we sync the `espanso/` folder.

### Synchronization
Syncthing keeps folders in sync automatically when:
- Files are added, modified, or deleted
- Devices are online and connected
- Folders are shared between devices

## Getting Your Device ID

### Windows
1. Open a browser and navigate to `http://localhost:8384`
2. Click **Actions** → **Show ID**
3. Copy the Device ID

### macOS
1. Open browser and go to `http://localhost:8384`
2. Click **Actions** → **Show ID**
3. Copy the Device ID

### Raspberry Pi
```bash
# Via command line
syncthing -device-id

# Or via web interface
# Navigate to http://[RPI-IP]:8384
# Click Actions → Show ID
```

## Adding Devices

### From Windows

1. Open Syncthing web UI (`http://localhost:8384`)
2. Click **"Add Remote Device"** in the bottom right
3. Enter the Device ID from another device
4. Give it a name (e.g., "MacBook Pro", "Raspberry Pi")
5. Under **Sharing**, select **"Espanso Config"** folder
6. Click **"Save"**
7. Accept the connection on the remote device

### From macOS

Same process as Windows:
1. Open `http://localhost:8384`
2. Click **"Add Remote Device"**
3. Enter Device ID
4. Name the device
5. Select folders to share
6. Save and accept on remote device

### From Raspberry Pi

Same process via web interface at `http://[RPI-IP]:8384`

## Folder Configuration

### Basic Folder Settings

1. Click on the folder name in Syncthing UI
2. Click **"Edit"**
3. Configure these settings:

#### General Tab
- **Folder Label**: User-friendly name (e.g., "Espanso Config")
- **Folder ID**: Unique identifier (e.g., `espanso-config`)
- **Folder Path**: Absolute path to the folder

#### Sharing Tab
- Select which devices should have access to this folder
- All devices should have the same folder to maintain sync

#### File Versioning Tab (Recommended)
Configure versioning to protect against accidental deletions:

**Simple File Versioning** (Recommended):
- **Keep Versions**: 10
- Old versions are kept in `.stversions` folder
- Easy to restore accidentally deleted files

**Staggered File Versioning** (Advanced):
- More sophisticated version retention
- Keeps different versions for different time periods

**Trash Can File Versioning**:
- Keeps deleted files for a specified number of days
- Simpler than staggered versioning

#### Advanced Tab
Important settings:
- **Folder Type**: Send & Receive (default)
- **Rescan Interval**: 60 seconds (default is fine)
- **Ignore Patterns**: Add patterns for files to exclude

### Ignore Patterns

Add these patterns to ignore temporary and backup files:

```
// Temporary files
*.tmp
*.temp
~*
.DS_Store
Thumbs.db

// Backup files
*.bak
*.backup
*~

// Version control
.git
.gitignore

// Editor files
.vscode
.idea
*.swp
*.swo
```

To add ignore patterns:
1. Edit folder
2. Go to **"Ignore Patterns"** tab
3. Add patterns (one per line)
4. Click **"Save"**

## Advanced Configuration

### Connection Settings

#### For Local Network Only

If all devices are on the same network:

1. Go to **Actions** → **Settings** → **Connections**
2. Configure:
   - **Enable NAT Traversal**: Yes
   - **Enable Local Discovery**: Yes
   - **Enable Global Discovery**: No (for local only)
   - **Enable Relaying**: No (for local only)

#### For Internet Synchronization

If devices need to sync over the internet:

1. Go to **Actions** → **Settings** → **Connections**
2. Configure:
   - **Enable NAT Traversal**: Yes
   - **Enable Global Discovery**: Yes
   - **Enable Relaying**: Yes (fallback if direct connection fails)

### Security Settings

#### Enable Authentication

Protect your Syncthing web interface:

1. Go to **Actions** → **Settings** → **GUI**
2. Set **GUI Authentication User** and **Password**
3. Save and reload

#### HTTPS for Web Interface

For secure web access:

1. Generate SSL certificate:
   ```bash
   # On Linux/Mac
   openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes
   ```

2. Configure in Syncthing:
   - Go to **Actions** → **Settings** → **GUI**
   - Enable **Use HTTPS for GUI**
   - Provide paths to certificate files

#### Device Encryption

Encrypt data between devices:

1. Edit folder
2. Go to **Advanced** tab
3. Enable **Untrusted** for devices that should receive encrypted data
4. Set encryption password
5. Remote device must enter same password

### Performance Tuning

#### For Low-Power Devices (Raspberry Pi)

Reduce resource usage:

1. **Actions** → **Settings** → **Connections**
   - Reduce **Max Recv Kbps** and **Max Send Kbps**

2. **Actions** → **Advanced**
   - Reduce **Max Concurrent Scans**
   - Increase **Folder Rescan Interval**

#### For Fast Networks

Maximize sync speed:

1. **Actions** → **Settings** → **Connections**
   - Increase bandwidth limits
   - Enable **TCP Keep-Alive**

2. **Actions** → **Advanced**
   - Increase **Max Concurrent Scans**

### Bandwidth Limiting

Limit bandwidth usage:

1. **Actions** → **Settings** → **Connections**
2. Set **Download Rate** and **Upload Rate**
3. Use **0** for unlimited

### Scheduled Sync

To sync only during specific hours:

**Option 1: Pause/Resume via Web UI**
- Click **Pause All** when not needed
- Resume when ready to sync

**Option 2: Schedule with System Tools**

On Linux/Mac (using cron):

First, create a secure script to store your API key:

```bash
# Create secure credentials directory
mkdir -p ~/.config/med_auto
chmod 700 ~/.config/med_auto

# Store your API key (get it from Actions → Settings → GUI → API Key)
echo "YOUR-API-KEY-HERE" > ~/.config/med_auto/syncthing_api
chmod 600 ~/.config/med_auto/syncthing_api
```

Create a script for cron:

```bash
# Create the script
cat > ~/.config/med_auto/syncthing-control.sh << 'EOF'
#!/bin/bash
API_KEY=$(cat ~/.config/med_auto/syncthing_api)
curl -X POST -H "X-API-Key: $API_KEY" http://localhost:8384/rest/system/$1
EOF

chmod +x ~/.config/med_auto/syncthing-control.sh
```

Then add to crontab:

```bash
# Pause Syncthing at 11 PM
0 23 * * * ~/.config/med_auto/syncthing-control.sh pause

# Resume at 7 AM
0 7 * * * ~/.config/med_auto/syncthing-control.sh resume
```

## Monitoring and Troubleshooting

### Check Sync Status

#### Via Web Interface
- **Green**: In sync
- **Yellow**: Syncing
- **Red**: Error or disconnected

#### Via Command Line
```bash
# On Linux/Mac
# First ensure your API key is stored securely (see scheduling section above)
API_KEY=$(cat ~/.config/med_auto/syncthing_api)
curl -H "X-API-Key: $API_KEY" http://localhost:8384/rest/system/status | jq
```

### Common Issues

#### Devices Not Connecting

**Check 1: Verify Device IDs**
- Ensure Device IDs are correct
- Case-sensitive

**Check 2: Firewall**
- Open port 22000 (TCP/UDP)
- Open port 21027 (UDP) for discovery

**Check 3: Network**
- Ensure devices are on the same network (for local)
- Enable Global Discovery for internet sync

#### Files Not Syncing

**Check 1: Folder Status**
- Click on folder to see status
- Check for errors

**Check 2: Permissions**
- Ensure read/write permissions on folder
- Check file ownership

**Check 3: Ignore Patterns**
- Verify file isn't ignored
- Check `.stignore` file

#### Out of Sync Errors

**Solution 1: Override Changes**
```
1. Click folder → Advanced → Override Changes
2. This forces the local version to remote
```

**Solution 2: Revert Local Changes**
```
1. Click folder → Advanced → Revert Local Changes
2. This accepts the remote version
```

#### High CPU/Memory Usage

**Reduce Resource Usage:**
1. Increase rescan interval
2. Reduce max concurrent scans
3. Use ignore patterns for large folders
4. Disable file watching on slow systems

### View Logs

#### Windows (Native)
```powershell
# If you started Syncthing manually, logs are in the console.
# If you used Task Scheduler or NSSM, check:
Get-Content $env:LOCALAPPDATA\Syncthing\syncthing.log -Tail 100
```

#### macOS/Linux
```bash
# Recent logs
journalctl --user -u syncthing -n 100

# Follow logs in real-time
journalctl --user -u syncthing -f
```

#### Via Web Interface
- **Actions** → **Logs**

## Backup and Recovery

### Backup Syncthing Configuration

```bash
# Linux/Mac
cp -r ~/.config/syncthing ~/syncthing-backup

# Windows
xcopy "%LOCALAPPDATA%\Syncthing" "C:\syncthing-backup" /E /I
```

### Restore Configuration

```bash
# Linux/Mac
cp -r ~/syncthing-backup/* ~/.config/syncthing/

# Windows
xcopy "C:\syncthing-backup" "%LOCALAPPDATA%\Syncthing" /E /I
```

### Recover Deleted Files

If file versioning is enabled:

1. Navigate to synced folder
2. Look for `.stversions` directory
3. Find the file you need
4. Copy back to main folder

## Integration with Espanso

### Workflow for Snippet Updates

1. **Make changes** to snippet files on any device
2. **Syncthing automatically syncs** the changes
3. **Restart Espanso** to load new snippets:
   ```bash
   # Windows/Mac
   espanso restart
   ```

### Automatic Espanso Restart (Advanced)

Create a file watcher to restart Espanso when files change:

#### macOS (using fswatch)
```bash
brew install fswatch

# Create watch script
cat > ~/watch-espanso.sh << 'EOF'
#!/bin/bash
fswatch -o ~/Documents/med_auto/espanso/match | while read num
do
  sleep 2  # Wait for sync to complete
  espanso restart
done
EOF

chmod +x ~/watch-espanso.sh
```

#### Linux (using inotifywait)
```bash
sudo apt install inotify-tools

# Create watch script
while inotifywait -r -e modify ~/med_auto/espanso/match; do
  sleep 2
  espanso restart
done
```

## Best Practices

### Do's
✅ Use file versioning
✅ Set strong GUI password
✅ Regularly check sync status
✅ Use ignore patterns for unnecessary files
✅ Keep Syncthing updated
✅ Test restores periodically

### Don'ts
❌ Don't sync system folders
❌ Don't ignore sync conflicts
❌ Don't share folders with untrusted devices
❌ Don't disable security features on public networks
❌ Don't store passwords in synced files (use encryption)

## Resources

- [Syncthing Documentation](https://docs.syncthing.net/)
- [Syncthing Community Forum](https://forum.syncthing.net/)
- [Syncthing GitHub](https://github.com/syncthing/syncthing)
- [Security Best Practices](https://docs.syncthing.net/users/security.html)

## Next Steps

- Configure specific devices: [Windows](setup-windows.md) | [macOS](setup-macos.md) | [Raspberry Pi](setup-raspberry-pi.md)
- Customize Espanso snippets in `espanso/match/`
- Set up automated backups
- Configure file versioning