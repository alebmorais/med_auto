# Raspberry Pi Setup Guide

This guide will help you set up the Raspberry Pi as a central hub for the cross-platform snippet system, serving as both a Syncthing node and a web-accessible file browser.

## Prerequisites

- Raspberry Pi (3, 4, or 5 recommended)
- Raspberry Pi OS (formerly Raspbian) installed
- Internet connection (WiFi or Ethernet)
- SSH access enabled (for remote setup)
- At least 8GB SD card (16GB+ recommended)

## Step 1: Prepare Raspberry Pi

### Update System

```bash
sudo apt update
sudo apt upgrade -y
```

### Create Directory Structure

```bash
# Create a directory for the synced Espanso configuration
mkdir -p ~/med_auto
cd ~/med_auto
```

## Step 2: Install and Configure Syncthing

### Install Syncthing

#### Option A: From Official Repository (Recommended)

```bash
# Add Syncthing GPG key
sudo mkdir -p /etc/apt/keyrings
sudo curl -L -o /etc/apt/keyrings/syncthing-archive-keyring.gpg https://syncthing.net/release-key.gpg

# Add Syncthing repository
echo "deb [signed-by=/etc/apt/keyrings/syncthing-archive-keyring.gpg] https://apt.syncthing.net/ syncthing stable" | sudo tee /etc/apt/sources.list.d/syncthing.list

# Update and install
sudo apt update
sudo apt install syncthing -y
```

#### Option B: From Raspberry Pi Repository

```bash
sudo apt install syncthing -y
```

### Configure Syncthing as a Service

```bash
# Enable Syncthing to start on boot
systemctl --user enable syncthing.service
systemctl --user start syncthing.service
```

### Locate Syncthing Configuration

First, find where Syncthing stores its config (paths vary by installation method):

```bash
syncthing -paths
```

Look for "Configuration file". Common locations:
- `$HOME/.config/syncthing/config.xml` (older installs)
- `$HOME/.local/state/syncthing/config.xml` (newer XDG-compliant installs)

If the directory doesn't exist, generate it:

```bash
# Option 1: Generate manually
syncthing -generate="$HOME/.local/state/syncthing"

# Option 2: Start once then stop
systemctl --user start syncthing
sleep 5
systemctl --user stop syncthing
```

### Configure Syncthing for Secure Access (Recommended: SSH Tunnel)

**Best practice:** Keep Syncthing bound to localhost (127.0.0.1) and access remotely via SSH tunnel.

1. **Stop Syncthing before editing**:
   ```bash
   systemctl --user stop syncthing
   ```

2. **Back up config**:
   ```bash
   # Use the path from syncthing -paths
   cp ~/.local/state/syncthing/config.xml ~/.local/state/syncthing/config.xml.bak
   ```

3. **Edit config** (replace path if yours differs):
   ```bash
   nano ~/.local/state/syncthing/config.xml
   ```

4. **Verify or add the GUI section** (keep localhost-only):
   ```xml
   <gui enabled="true" tls="false">
     <address>127.0.0.1:8384</address>
     <apikey></apikey>
     <theme>default</theme>
   </gui>
   ```

5. **Restart Syncthing**:
   ```bash
   systemctl --user start syncthing
   ```

6. **Verify bind address**:
   ```bash
   ss -tuln | grep :8384
   # Should show: 127.0.0.1:8384 (localhost-only, secure)
   ```

### Access Syncthing Remotely via SSH Tunnel

From your laptop/desktop (replace `[RPI-IP]` and `pi` with your Pi's IP and username):

```bash
ssh -L 8384:localhost:8384 pi@[RPI-IP]
```

Then open on your local machine:
- http://localhost:8384

**Alternative (not recommended):** If you absolutely need LAN access:
- Change `<address>` to `0.0.0.0:8384`
- Set a strong GUI username/password immediately in the web UI
- Configure firewall: `sudo ufw allow from 192.168.1.0/24 to any port 8384 proto tcp`

### Access Syncthing Web Interface

**From the Pi** (local):
```bash
# Option 1: Use a text browser on the Pi
lynx http://localhost:8384

# Option 2: If Pi has a desktop environment
xdg-open http://localhost:8384
```

**From another computer** (via SSH tunnel - recommended):
```bash
# On your laptop/desktop:
ssh -L 8384:localhost:8384 pi@[RPI-IP]

# Then open in your browser:
# http://localhost:8384
```

**Important:** Set a strong GUI username/password:
- In the web UI: Actions → Settings → GUI
- Set Username and Password
- Save and restart if prompted

### Configure Syncthing Folder

1. In the web interface, click **"Add Folder"**
2. Configure:
   - **Folder Label**: "Espanso Config"
   - **Folder Path**: `/home/pi/med_auto/espanso`
   - **Folder ID**: `espanso-config`
3. Under **"File Versioning"**, enable versioning (recommended):
   - **Type**: Simple File Versioning
   - **Keep Versions**: 10
4. Click **"Save"**

### Add Remote Devices

1. Get Device IDs from Windows/Mac devices
2. Click **"Add Remote Device"**
3. Enter Device ID
4. Share "Espanso Config" folder
5. Click **"Save"**

## Step 3: Install and Configure File Browser

### Install File Browser

```bash
# Download and install File Browser
curl -fsSL https://raw.githubusercontent.com/filebrowser/get/master/get.sh | bash
```

### Create File Browser Configuration

```bash
# Create config directory
mkdir -p ~/.config/filebrowser

# Create configuration file
# Security Note: Using 127.0.0.1 (localhost-only) by default for safety
# Adjust paths if your username isn't 'pi' (use $HOME for current user)
cat > ~/.config/filebrowser/filebrowser.json << 'EOF'
{
  "port": 8080,
  "baseURL": "",
  "address": "127.0.0.1",
  "log": "stdout",
  "database": "$HOME/.config/filebrowser/filebrowser.db",
  "root": "$HOME/med_auto/espanso"
}
EOF

# Replace $HOME with actual path
sed -i "s|\$HOME|$HOME|g" ~/.config/filebrowser/filebrowser.json
```

### Secure File Browser Admin Account

By default, File Browser might create an admin account without a password. Lock it down:

```bash
# Stop the service if it's running
sudo systemctl stop filebrowser 2>/dev/null || true

# Set admin password (change 'YourStrongPass123!' to your own)
export FILEBROWSER_DB="$HOME/.config/filebrowser/filebrowser.db"
filebrowser -d "$FILEBROWSER_DB" users update admin \
  --password 'YourStrongPass123!' \
  --perm.admin \
  --scope "$HOME/med_auto/espanso"

# If admin doesn't exist yet, create it:
# filebrowser -d "$FILEBROWSER_DB" users add admin 'YourStrongPass123!' \
#   --perm.admin \
#   --scope "$HOME/med_auto/espanso"

# Verify
filebrowser -d "$FILEBROWSER_DB" users list
```

### Create Systemd Service for File Browser

```bash
# Create service file
sudo nano /etc/systemd/system/filebrowser.service
```

Add the following content:
```ini
[Unit]
Description=File Browser
After=network.target

[Service]
Type=simple
User=pi
Group=pi
ExecStart=/usr/local/bin/filebrowser -c /home/pi/.config/filebrowser/filebrowser.json
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
```

### Enable and Start File Browser

```bash
# Reload systemd
sudo systemctl daemon-reload

# Enable File Browser to start on boot
sudo systemctl enable filebrowser

# Start File Browser
sudo systemctl start filebrowser

# Check status
sudo systemctl status filebrowser
```

### Access File Browser

1. Open browser: `http://localhost:8080` (from the Pi) or `http://[RPI-IP-ADDRESS]:8080` (if address is set to 0.0.0.0)
2. Default credentials:
   - Username: `admin`
   - Password: `admin`
3. **Important**: Change password immediately after first login!

### Configure File Browser

1. Login to File Browser
2. Go to **Settings** → **User Management**
3. Change admin password
4. Configure user permissions as needed

## Step 4: Clone Repository (Optional)

If you want to initialize with this repository:

```bash
cd ~/med_auto
git clone https://github.com/alebmorais/med_auto.git .

# Or download and extract if Git is not available
wget https://github.com/alebmorais/med_auto/archive/refs/heads/main.zip
unzip main.zip
mv med_auto-main/* .
rm -rf med_auto-main main.zip
```

## Step 5: Set Up Firewall (Optional but Recommended)

### Install UFW

```bash
sudo apt install ufw -y
```

### Configure Firewall Rules

```bash
# Allow SSH
sudo ufw allow 22/tcp

# Allow Syncthing
sudo ufw allow 8384/tcp  # Web UI
sudo ufw allow 22000/tcp # Sync protocol
sudo ufw allow 22000/udp # Sync protocol
sudo ufw allow 21027/udp # Discovery

# Allow File Browser
sudo ufw allow 8080/tcp

# Enable firewall
sudo ufw enable
```

## Step 6: Configure Static IP (Optional)

For consistent access, set a static IP:

### Edit DHCP Configuration

```bash
sudo nano /etc/dhcpcd.conf
```

Add at the end (adjust values for your network):
```
interface eth0
static ip_address=192.168.1.100/24
static routers=192.168.1.1
static domain_name_servers=192.168.1.1 8.8.8.8

interface wlan0
static ip_address=192.168.1.100/24
static routers=192.168.1.1
static domain_name_servers=192.168.1.1 8.8.8.8
```

Reboot:
```bash
sudo reboot
```

## Step 7: Test the Setup

### Verify Syncthing

1. Access Syncthing UI:
   - If using localhost binding (default): SSH into Pi and use `lynx http://localhost:8384` or create SSH tunnel
   - If using 0.0.0.0 binding: `http://[RPI-IP]:8384`
2. Check that devices are connected
3. Verify folder is syncing

### Verify File Browser

1. Access File Browser:
   - If using localhost binding (default): Create SSH tunnel or access from Pi directly
   - If using 0.0.0.0 binding: `http://[RPI-IP]:8080`
2. Navigate to the espanso folder
3. Try creating a test file
4. Verify it syncs to other devices

### SSH Tunnel Example (Recommended for Remote Access)

For secure remote access without exposing services:

```bash
# From your local machine, create SSH tunnel
ssh -L 8384:localhost:8384 -L 8080:localhost:8080 pi@[RPI-IP]

# Then access via localhost:
# Syncthing: http://localhost:8384
# File Browser: http://localhost:8080
```

### Test Snippet Creation via File Browser

1. Login to File Browser
2. Navigate to `match/personal/`
3. Create a new file `test.yml`
4. Add content:
   ```yaml
   matches:
     - trigger: ":test"
       replace: "Test from Raspberry Pi"
   ```
5. Save the file
6. Wait for sync
7. Restart Espanso on Windows/Mac
8. Test the trigger

## Monitoring and Maintenance

### Check Syncthing Status

```bash
systemctl --user status syncthing
```

### Check File Browser Status

```bash
sudo systemctl status filebrowser
```

### View Syncthing Logs

```bash
journalctl --user -u syncthing -f
```

### View File Browser Logs

```bash
sudo journalctl -u filebrowser -f
```

### Restart Services

```bash
# Restart Syncthing
systemctl --user restart syncthing

# Restart File Browser
sudo systemctl restart filebrowser
```

## Backup Strategy

### Automatic Versioning

Syncthing already provides file versioning (configured earlier). You can also:

### Manual Backup Script

Create a backup script:

```bash
nano ~/backup-espanso.sh
```

Add:
```bash
#!/bin/bash
BACKUP_DIR=~/backups/espanso
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p $BACKUP_DIR
tar -czf $BACKUP_DIR/espanso_backup_$DATE.tar.gz ~/med_auto/espanso

# Keep only last 7 backups
cd $BACKUP_DIR
ls -t | tail -n +8 | xargs rm -f
```

Make executable:
```bash
chmod +x ~/backup-espanso.sh
```

### Schedule with Cron

```bash
crontab -e
```

Add (daily backup at 2 AM):
```
0 2 * * * /home/pi/backup-espanso.sh
```

## Security Considerations

### Change Default Passwords

- File Browser: Change from default `admin`/`admin`
- Syncthing: Set admin password in web UI

### Enable HTTPS (Optional)

For secure access over the internet, consider:
- Setting up reverse proxy (nginx)
- Using Let's Encrypt for SSL certificates
- Or use Syncthing's built-in encryption

### Limit Access

- Use firewall rules to restrict access
- Consider VPN for remote access
- Enable Syncthing authentication

## Troubleshooting

### Syncthing Not Starting

```bash
# Check status
systemctl --user status syncthing

# Check logs
journalctl --user -u syncthing -n 50
```

### File Browser Not Accessible

```bash
# Check if running
sudo systemctl status filebrowser

# Check if port is listening
sudo netstat -tulpn | grep 8080

# Check logs
sudo journalctl -u filebrowser -n 50
```

### Permission Issues

```bash
# Fix ownership
sudo chown -R pi:pi ~/med_auto

# Fix permissions
chmod -R 755 ~/med_auto
```

## Advanced Configuration

### Custom Domain

Use a service like DuckDNS for a custom domain:

```bash
# Install DuckDNS update script
mkdir ~/duckdns
cd ~/duckdns
nano duck.sh
```

Add your DuckDNS configuration and set up cron.

### Port Forwarding

For access outside your network:
1. Configure port forwarding on your router
2. Forward ports 8384 and 8080 to Raspberry Pi
3. Use strong passwords and consider VPN

## Next Steps

- Set up Windows client (see [setup-windows.md](setup-windows.md))
- Set up macOS client (see [setup-macos.md](setup-macos.md))
- Advanced Syncthing configuration (see [syncthing-setup.md](syncthing-setup.md))

## Resources

- [Raspberry Pi Documentation](https://www.raspberrypi.org/documentation/)
- [Syncthing Documentation](https://docs.syncthing.net/)
- [File Browser Documentation](https://filebrowser.org/)