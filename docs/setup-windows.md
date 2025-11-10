# Windows Setup Guide

This guide will help you set up the cross-platform snippet system on Windows.

## Prerequisites

- Windows 10 or later
- Administrator access for installation
- Internet connection for downloading software

## Step 1: Install Espanso

### Download and Install

1. Visit the [Espanso releases page](https://github.com/espanso/espanso/releases)
2. Download the latest Windows installer (`.exe` file)
3. Run the installer and follow the installation wizard
4. Espanso will be installed and started automatically

### Verify Installation

Open PowerShell and run:
```powershell
espanso --version
```

You should see the version number displayed.

## Step 2: Install Syncthing

### Download and Install

1. Visit [Syncthing Downloads](https://syncthing.net/downloads/)
2. Download the Windows installer
3. Run the installer
4. Syncthing will start automatically and open the web interface

### Run Syncthing at Startup (Recommended)

Keep Syncthing running automatically after login while following secure defaults.

#### Option A: Task Scheduler (no extra tools)

1. Open Task Scheduler → Create Task
2. General tab:
   - Name: "Syncthing"
   - Check "Run whether user is logged on or not"
   - Optional: "Run with highest privileges"
3. Triggers tab: New → "At log on" → Any user
4. Actions tab: New → "Start a program"
   - Program/script: `C:\\Users\\YourName\\AppData\\Local\\Syncthing\\syncthing.exe`
   - Add arguments (optional): `-no-console -logfile %LOCALAPPDATA%\Syncthing\syncthing.log`
   - Start in: `C:\\Users\\YourName\\AppData\\Local\\Syncthing`
5. Conditions/Settings: adjust to your preference → OK → provide credentials

Logs (if configured): `%LOCALAPPDATA%\Syncthing\syncthing.log`

#### Option B: Windows Service via NSSM (advanced)

1. Download NSSM: https://nssm.cc/download
2. Install service (in an elevated PowerShell):

```powershell
# Adjust paths as needed
$nssm = "C:\\Tools\\nssm.exe"
$exe  = "$env:LOCALAPPDATA\\Syncthing\\syncthing.exe"
& $nssm install Syncthing $exe -no-console -logfile $env:LOCALAPPDATA\Syncthing\syncthing.log
& $nssm set Syncthing AppDirectory $env:LOCALAPPDATA\Syncthing
& $nssm start Syncthing
```

Service management: `services.msc` (look for "Syncthing").

### Secure the Syncthing GUI

After first start, open `http://localhost:8384` and configure:

1. Actions → Settings → GUI
2. Set GUI Listen Address: `127.0.0.1:8384` (default/local-only)
3. Set GUI Authentication: username + strong password
4. (Optional) Enable HTTPS for the GUI (self‑signed cert)
5. Save → restart Syncthing if prompted

Note: Keep the GUI bound to localhost. For remote access, use an SSH tunnel or a reverse proxy with TLS.

## Step 3: Clone or Download This Repository

### Option A: Using Git

If you have Git installed:
```powershell
cd %USERPROFILE%\Documents
git clone https://github.com/alebmorais/med_auto.git
```

### Option B: Download ZIP

1. Download this repository as a ZIP file
2. Extract to a location like `C:\Users\YourName\Documents\med_auto`

## Step 4: Configure Espanso

### Verify Espanso Paths

First, check where Espanso stores its configuration:

```powershell
espanso path
```

You'll see output like:
```
Config: C:\Users\YourName\AppData\Roaming\espanso
Packages: C:\Users\YourName\AppData\Roaming\espanso\match\packages
Runtime: C:\Users\YourName\AppData\Local\espanso
```

### Set Up Configuration Directory

1. Open PowerShell as Administrator (Win+X → "Terminal (Admin)" or "PowerShell (Admin)")
2. Create a symbolic link or copy the configuration:

#### Option A: Symbolic Link (Recommended for Auto-Sync)

```powershell
# Navigate to Espanso config directory
cd $env:APPDATA\espanso

# Back up existing config if it exists
if (Test-Path "config") { Move-Item "config" "config.bak.$(Get-Date -Format 'yyyy-MM-dd-HHmmss')" }
if (Test-Path "match") { Move-Item "match" "match.bak.$(Get-Date -Format 'yyyy-MM-dd-HHmmss')" }

# Create symbolic links (replace YourName with your actual username, or use full path)
New-Item -ItemType SymbolicLink -Path "config" -Target "$env:USERPROFILE\Documents\med_auto\espanso\config"
New-Item -ItemType SymbolicLink -Path "match" -Target "$env:USERPROFILE\Documents\med_auto\espanso\match"
```

**Example with actual path** (if your username is `alemo`):
```powershell
New-Item -ItemType SymbolicLink -Path "config" -Target "C:\Users\alemo\Documents\med_auto\espanso\config"
New-Item -ItemType SymbolicLink -Path "match" -Target "C:\Users\alemo\Documents\med_auto\espanso\match"
```

**Verify links:**
```powershell
Get-ChildItem -Force
# You should see config and match with LinkType = SymbolicLink
```

#### Option B: Copy Files
```powershell
# Copy configuration files
Copy-Item -Path "C:\Users\YourName\Documents\med_auto\espanso\*" -Destination "$env:APPDATA\espanso" -Recurse -Force
```

### Restart Espanso

```powershell
espanso restart
```

## Step 5: Configure Syncthing

### Access Syncthing Web Interface

1. Open your web browser
2. Navigate to `http://localhost:8384`
3. The Syncthing web interface will open

### Add Folder to Sync

1. Click **"Add Folder"**
2. Set the following:
   - **Folder Label**: "Espanso Config"
   - **Folder Path**: `C:\Users\YourName\Documents\med_auto\espanso`
   - **Folder ID**: `espanso-config` (or auto-generated)
3. Click **"Save"**

### Add Remote Devices

1. Get the Device ID from your other devices (Mac, Raspberry Pi)
2. In Syncthing, click **"Add Remote Device"**
3. Enter the Device ID
4. Select the "Espanso Config" folder to share
5. Click **"Save"**
6. Accept the connection on the remote device

## Step 6: Test Your Setup

### Test Espanso

1. Open any text editor (Notepad, Word, etc.)
2. Type a trigger, for example: `:bp`
3. It should expand to "Blood Pressure"

### Common Test Triggers

- `:date` - Inserts current date
- `:email` - Inserts email template
- `:hi` - Inserts "Hello,"
- `:meeting` - Inserts meeting notes template

### Verify Syncthing

1. Create a new snippet file on another device
2. Wait for Syncthing to sync (usually within seconds)
3. Restart Espanso on Windows: `espanso restart`
4. Test the new snippet

## Troubleshooting

### Espanso Not Expanding

1. Check if Espanso is running:
   ```powershell
   espanso status
   ```
2. Restart Espanso:
   ```powershell
   espanso restart
   ```
3. Check the logs:
   ```powershell
   espanso log
   ```

### Syncthing Not Syncing

1. Check Syncthing status in the web interface
2. Ensure all devices are connected
3. Check folder path is correct
4. Look for errors in the Syncthing logs

### Snippets Not Working After Sync

1. Restart Espanso after sync completes
2. Check file permissions
3. Verify YAML syntax in snippet files

## Advanced Configuration

### Custom Triggers

Edit `espanso/config/default.yml` to customize:
- Toggle key
- Search trigger
- Paste behavior
- Backend (Inject vs Clipboard)

### Adding Custom Snippets

1. Navigate to `med_auto/espanso/match/`
2. Choose a category or create a new one
3. Add your snippets following the YAML format
4. Restart Espanso

Example:
```yaml
matches:
  - trigger: ":mysnippet"
    replace: "My custom text"
```

## Security Considerations

### Windows Defender

Espanso may trigger Windows Defender. Add Espanso to the exclusions list if needed:

1. Open Windows Security
2. Go to Virus & threat protection settings
3. Add exclusion for Espanso directory

### Firewall

If using Syncthing across networks, ensure firewall allows:
- Port 22000 (TCP/UDP) for sync
- Port 21027 (UDP) for discovery

## Next Steps

- Customize your snippets in the `espanso/match/` folders
- Set up on macOS (see [setup-macos.md](setup-macos.md))
- Set up Raspberry Pi server (see [setup-raspberry-pi.md](setup-raspberry-pi.md))
- Configure additional Syncthing options (see [syncthing-setup.md](syncthing-setup.md))

## Resources

- [Espanso Documentation](https://espanso.org/docs/)
- [Espanso Windows Guide](https://espanso.org/docs/install/win/)
- [Syncthing Getting Started](https://docs.syncthing.net/intro/getting-started.html)
