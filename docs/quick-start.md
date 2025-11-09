# Quick Start Guide

Get your cross-platform snippet system up and running in minutes!

## 🚀 5-Minute Setup

### Step 1: Install Software (5 minutes)

Choose your platform:

#### Windows
```powershell
# Download and install:
# 1. Espanso: https://espanso.org/install/win/
# 2. Syncthing (Windows): https://syncthing.net/downloads/
# Optional: schedule Syncthing at login using Task Scheduler
```

#### macOS
```bash
# Using Homebrew
brew tap espanso/espanso
brew install espanso syncthing

# Register and start Espanso
espanso service register
espanso start

# Start Syncthing
brew services start syncthing
```

#### Raspberry Pi (Server)
```bash
# Install Syncthing
sudo apt update
sudo apt install syncthing -y

# Install File Browser
curl -fsSL https://raw.githubusercontent.com/filebrowser/get/master/get.sh | bash

# Enable Syncthing
systemctl --user enable syncthing
systemctl --user start syncthing
```

### Step 2: Get This Repository (2 minutes)

```bash
# Clone repository
git clone https://github.com/alebmorais/med_auto.git

# Or download ZIP and extract
```

### Step 3: Configure Espanso (2 minutes)

#### Windows
```powershell
# In PowerShell (Admin)
cd $env:APPDATA\espanso
New-Item -ItemType SymbolicLink -Path "config" -Target "C:\path\to\med_auto\espanso\config"
New-Item -ItemType SymbolicLink -Path "match" -Target "C:\path\to\med_auto\espanso\match"
espanso restart
```

#### macOS
```bash
cd ~/Library/Application\ Support/espanso
ln -s ~/path/to/med_auto/espanso/config config
ln -s ~/path/to/med_auto/espanso/match match
espanso restart
```

### Step 4: Configure Syncthing (3 minutes)

1. Open Syncthing web UI: `http://localhost:8384`
2. Add folder:
   - Label: "Espanso Config"
   - Path: `/path/to/med_auto/espanso`
3. Add remote devices (get Device ID from each device)
4. Share the "Espanso Config" folder with all devices
5. Accept connections on each device

### Step 5: Test! (1 minute)

Open any text editor and type:
- `:date` → Should insert current date
- `:email` → Should insert email template
- `:bp` → Should expand to "Blood Pressure"

## ✅ Verification Checklist

- [ ] Espanso installed and running
- [ ] Syncthing installed and running
- [ ] Repository cloned/downloaded
- [ ] Espanso configured to use repository snippets
- [ ] Syncthing folder added and syncing
- [ ] All devices connected in Syncthing
- [ ] Test snippets working on each device

## 🔧 Common First-Time Issues

### Espanso Not Expanding

**Fix:**
```bash
# Check status
espanso status

# Restart
espanso restart

# Check permissions (macOS)
# System Preferences → Security & Privacy → Accessibility
```

### Devices Not Connecting

**Fix:**
1. Get Device ID: Syncthing → Actions → Show ID
2. Add device on both sides
3. Accept connection request
4. Check firewall (allow port 22000)

### Changes Not Syncing

**Fix:**
1. Check Syncthing web UI - both devices should be "Up to Date"
2. Restart Espanso after sync completes
3. Check file permissions

## 📱 Using Your Snippets

### Basic Usage

Type trigger + space/enter:
```
:bp → Blood Pressure
:email → your.email@example.com
:date → 2025-11-05
```

### Searching Snippets

Press `ALT+SPACE` to search available snippets

### Customizing

Edit files in `espanso/match/` directories:
```yaml
matches:
  - trigger: ":mysnippet"
    replace: "My custom text"
```

Then restart Espanso:
```bash
espanso restart
```

## 🎯 Next Steps

1. **Customize snippets**: Edit files in `espanso/match/`
2. **Add categories**: Create new folders for your snippet types
3. **Read docs**: Check `docs/` folder for detailed guides
4. **Set up Raspberry Pi**: Optional central server (see `docs/setup-raspberry-pi.md`)

## 📚 Documentation

- [Windows Setup](docs/setup-windows.md) - Detailed Windows installation
- [macOS Setup](docs/setup-macos.md) - Detailed macOS installation
- [Raspberry Pi Setup](docs/setup-raspberry-pi.md) - Central server setup
- [Syncthing Setup](docs/syncthing-setup.md) - Advanced sync configuration
- [Customization Guide](docs/customization-guide.md) - Create custom snippets

## 💡 Pro Tips

1. **Backup**: Syncthing's file versioning is enabled by default
2. **Performance**: Disable categories you don't use
3. **Triggers**: Use `:` prefix to avoid accidental expansions
4. **Testing**: Test new snippets in a text editor first
5. **Syncing**: Always restart Espanso after adding snippets

## 🆘 Need Help?

- Check [Espanso Docs](https://espanso.org/docs/)
- Check [Syncthing Docs](https://docs.syncthing.net/)
- Review logs: `espanso log`
- Check Syncthing web UI for sync status

## 🎉 You're Done!

Your cross-platform snippet system is now set up and syncing across all your devices!

Start typing your triggers and enjoy the productivity boost! 🚀