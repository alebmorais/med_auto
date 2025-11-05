# macOS Setup Guide

This guide will help you set up the cross-platform snippet system on macOS.

## Prerequisites

- macOS 10.15 (Catalina) or later
- Administrator access
- Internet connection
- Homebrew (recommended) or manual installation

## Step 1: Install Espanso

### Option A: Using Homebrew (Recommended)

```bash
# Install Homebrew if not already installed
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Tap Espanso repository
brew tap espanso/espanso

# Install Espanso
brew install espanso
```

### Option B: Manual Installation

1. Visit the [Espanso releases page](https://github.com/espanso/espanso/releases)
2. Download the latest macOS installer (`.dmg` file)
3. Open the `.dmg` file
4. Drag Espanso to Applications
5. Open Espanso from Applications

### Register and Start Espanso

```bash
# Register Espanso as a service
espanso service register

# Start Espanso
espanso start
```

### Grant Accessibility Permissions

1. Go to **System Preferences** → **Security & Privacy** → **Privacy**
2. Click **Accessibility** in the left sidebar
3. Click the lock icon and enter your password
4. Add Espanso to the list and enable it
5. Repeat for **Input Monitoring** if prompted

### Verify Installation

```bash
espanso --version
```

## Step 2: Install Syncthing

### Option A: Using Homebrew (Recommended)

```bash
# Install Syncthing
brew install syncthing

# Start Syncthing service
brew services start syncthing
```

### Option B: Manual Installation

1. Visit [Syncthing Downloads](https://syncthing.net/downloads/)
2. Download the macOS package
3. Extract and move to Applications
4. Run Syncthing from Applications

### Option C: Syncthing-macOS (GUI App)

For a native macOS experience:

```bash
brew install --cask syncthing
```

Or download from [Syncthing-macOS releases](https://github.com/syncthing/syncthing-macos/releases)

## Step 3: Clone or Download This Repository

### Option A: Using Git

```bash
cd ~/Documents
git clone https://github.com/alebmorais/med_auto.git
```

### Option B: Download ZIP

1. Download this repository as a ZIP file
2. Extract to `~/Documents/med_auto`

## Step 4: Configure Espanso

### Set Up Configuration Directory

Open Terminal and run:

#### Option A: Symbolic Link (Recommended)

```bash
# Navigate to Espanso config directory
cd ~/Library/Application\ Support/espanso

# Backup existing config (if any)
mv config config.backup 2>/dev/null || true
mv match match.backup 2>/dev/null || true

# Create symbolic links
ln -s ~/Documents/med_auto/espanso/config config
ln -s ~/Documents/med_auto/espanso/match match
```

#### Option B: Copy Files

```bash
# Copy configuration files
cp -r ~/Documents/med_auto/espanso/* ~/Library/Application\ Support/espanso/
```

### Restart Espanso

```bash
espanso restart
```

## Step 5: Configure Syncthing

### Access Syncthing Web Interface

1. Open your web browser
2. Navigate to `http://localhost:8384`
3. The Syncthing web interface will open

### Add Folder to Sync

1. Click **"Add Folder"**
2. Configure the folder:
   - **Folder Label**: "Espanso Config"
   - **Folder Path**: `/Users/YourUsername/Documents/med_auto/espanso`
   - **Folder ID**: `espanso-config` (or auto-generated)
3. Click **"Save"**

### Add Remote Devices

1. Get Device IDs from your other devices (Windows, Raspberry Pi)
2. In Syncthing, click **"Add Remote Device"**
3. Enter the Device ID
4. Select the "Espanso Config" folder to share
5. Click **"Save"**
6. Accept the connection on the remote device

## Step 6: Test Your Setup

### Test Espanso

1. Open any text editor (TextEdit, Notes, VS Code, etc.)
2. Type a trigger: `:bp`
3. It should expand to "Blood Pressure"

### Common Test Triggers

- `:date` - Inserts current date
- `:time` - Inserts current time
- `:email` - Inserts email template
- `:meeting` - Inserts meeting notes template

### Verify Syncthing

1. Create or modify a snippet file on another device
2. Wait for Syncthing to sync (usually within seconds)
3. Restart Espanso: `espanso restart`
4. Test the synced snippet

## Troubleshooting

### Espanso Not Expanding

Check Espanso status:
```bash
espanso status
```

Restart Espanso:
```bash
espanso restart
```

Check logs:
```bash
espanso log
```

### Permission Issues

If Espanso doesn't work, verify permissions:

1. **System Preferences** → **Security & Privacy** → **Privacy**
2. Check **Accessibility**
3. Check **Input Monitoring**
4. Ensure Espanso is enabled in both

### Syncthing Not Running

Check if Syncthing is running:
```bash
brew services list | grep syncthing
```

Restart Syncthing:
```bash
brew services restart syncthing
```

### Symbolic Links Not Working

If symbolic links cause issues:
1. Remove the symbolic links
2. Use the copy method instead
3. Set up a cron job or script to sync periodically

### Snippets Not Working After Sync

Always restart Espanso after sync:
```bash
espanso restart
```

## Advanced Configuration

### Auto-Start Syncthing

Using Homebrew services:
```bash
brew services start syncthing
```

### Custom Espanso Triggers

Edit `~/Documents/med_auto/espanso/config/default.yml`:

```yaml
# Change toggle key
toggle_key: ALT

# Change search trigger
search_trigger: ":"

# Customize paste behavior
backend: Inject  # or Clipboard
```

### Adding Custom Snippets

1. Navigate to `~/Documents/med_auto/espanso/match/`
2. Choose a category directory
3. Create or edit `.yml` files
4. Follow the YAML format

Example snippet:
```yaml
matches:
  - trigger: ":mysnippet"
    replace: "My custom text"
  
  - trigger: ":multiline"
    replace: |
      Line 1
      Line 2
      Line 3
```

### Using Variables

Espanso supports dynamic content:

```yaml
matches:
  - trigger: ":now"
    replace: "{{mytime}}"
    vars:
      - name: mytime
        type: date
        params:
          format: "%H:%M:%S"
```

## Security Considerations

### macOS Security

macOS Gatekeeper may warn about Espanso:
1. If prompted, go to **System Preferences** → **Security & Privacy**
2. Click **"Open Anyway"** for Espanso

### Network Security

For Syncthing across networks:
- Port 22000 (TCP/UDP) for sync
- Port 21027 (UDP) for local discovery
- Configure macOS Firewall if needed

### Encryption

Enable Syncthing folder encryption for sensitive snippets:
1. Edit folder settings in Syncthing
2. Enable encryption
3. Set a strong password

## Performance Tips

### Reduce CPU Usage

If Espanso uses too much CPU:
1. Reduce the number of active matches
2. Use more specific triggers
3. Disable unused snippet categories

### Faster Sync

For faster Syncthing sync:
1. Use LAN connections when possible
2. Enable relay only if needed
3. Adjust sync interval in settings

## Next Steps

- Customize snippets in `espanso/match/` folders
- Set up on Windows (see [setup-windows.md](setup-windows.md))
- Set up Raspberry Pi server (see [setup-raspberry-pi.md](setup-raspberry-pi.md))
- Configure Syncthing options (see [syncthing-setup.md](syncthing-setup.md))

## Resources

- [Espanso Documentation](https://espanso.org/docs/)
- [Espanso macOS Guide](https://espanso.org/docs/install/mac/)
- [Syncthing Documentation](https://docs.syncthing.net/)
- [Homebrew](https://brew.sh/)
