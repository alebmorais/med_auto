# Med Auto - Cross-Platform Snippet System

A synchronized, cross-platform text snippet system using Espanso, Syncthing, and File Browser for seamless snippet management across Windows, Mac, and Raspberry Pi.

## 📋 Overview

This project provides a complete configuration for a multi-platform snippet expansion system with centralized storage and synchronization. The system consists of:

- **Espanso**: Universal text expander for Windows and macOS
- **Syncthing**: Decentralized file synchronization
- **File Browser**: Web-based file management interface (Raspberry Pi)
- **Organized Snippet Library**: Pre-configured snippets organized by category

## 🏗️ Architecture

```
┌─────────────┐      ┌─────────────┐      ┌─────────────────┐
│   Windows   │      │    macOS    │      │  Raspberry Pi   │
│             │      │             │      │                 │
│  Espanso    │◄────►│  Espanso    │◄────►│  File Browser   │
│  Syncthing  │      │  Syncthing  │      │  Syncthing      │
└─────────────┘      └─────────────┘      └─────────────────┘
       │                    │                      │
       └────────────────────┴──────────────────────┘
                    Synchronized espanso/
                     Configuration Folder
```

## 📁 Directory Structure

```
med_auto/
├── espanso/
│   ├── config/
│   │   └── default.yml          # Main Espanso configuration
│   └── match/                   # Snippet definitions
│       ├── atendimento/         # Portuguese customer service phrases
│       │   ├── inicio.yml
│       │   ├── finalizacao.yml
│       │   └── README.md
│       ├── medical/             # Medical terminology snippets
│       │   ├── common.yml
│       │   └── procedures.yml
│       ├── personal/            # Personal information snippets
│       │   └── common.yml
│       ├── productivity/        # Productivity templates
│       │   └── common.yml
│       └── development/         # Development/coding snippets
│           └── common.yml
├── docs/
│   ├── quick-start.md           # Quick start guide
│   ├── setup-windows.md         # Windows installation guide
│   ├── setup-macos.md           # macOS installation guide
│   ├── setup-raspberry-pi.md   # Raspberry Pi setup guide
│   ├── syncthing-setup.md      # Syncthing configuration guide
│   └── customization-guide.md  # Snippet customization
└── README.md
```

## 🚀 Quick Start

### Prerequisites

- **Windows/macOS**: Espanso + Syncthing
- **Raspberry Pi**: Syncthing + File Browser + web server

### Installation Steps

1. **Install Espanso** on your computers (Windows/macOS)
2. **Install Syncthing** on all devices
3. **Clone this repository** to each device
4. **Configure Syncthing** to sync the `espanso/` folder
5. **Set up Espanso** to use the synced configuration
6. **Configure File Browser** on Raspberry Pi (optional)

For detailed installation instructions, see the platform-specific guides in the `docs/` folder.

## 📝 Snippet Categories

### Atendimento (`match/atendimento/`)
- Portuguese customer service phrases
- Opening greetings (morning, afternoon, evening)
- Closing messages and farewells
- Standard customer interaction phrases

### Medical (`match/medical/`)
- Common medical abbreviations (BP, HR, Dx, Tx, etc.)
- Medical templates (vital signs, assessments)
- Standard medical phrases

### Personal (`match/personal/`)
- Contact information templates
- Email signatures
- Date/time insertions
- Common greetings

### Productivity (`match/productivity/`)
- Meeting notes templates
- Task management
- Email templates
- Weekly reports

### Development (`match/development/`)
- Git commands
- Code comments (TODO, FIXME, etc.)
- Documentation templates
- Debugging helpers

## 🔧 Usage

Snippets are triggered by typing the trigger keyword. All triggers start with `:` by default.

Examples:
- `:bp` → "Blood Pressure"
- `:date` → "2025-11-05"
- `:cumprimento-manha` → "Bom dia, tudo bem? Qual seria a solicitação, por gentileza?"
- `:despedida` → "Disponha! Bom plantão!"
- `:meeting` → Full meeting notes template
- `:gitcommit` → Git commit command

## 📖 Documentation

- [Windows Setup Guide](docs/setup-windows.md)
- [macOS Setup Guide](docs/setup-macos.md)
- [Raspberry Pi Setup Guide](docs/setup-raspberry-pi.md)
- [Syncthing Configuration](docs/syncthing-setup.md)

## 🔒 Security & Privacy

- All synchronization happens on your local network
- No cloud services required
- Full control over your data
- Encrypted sync with Syncthing (optional)

### Security Best Practices

**Important:** When deploying this system, especially on Raspberry Pi or shared networks, follow these security guidelines:

#### Network Binding
- **Default to localhost:** Services should bind to `127.0.0.1` (localhost) by default for security
- **Network exposure:** Only change to `0.0.0.0` if you need network access, and ensure:
  - Strong passwords are set immediately
  - Firewall rules restrict access to trusted devices/networks
  - Consider using a reverse proxy with TLS for remote access

#### API Key Management
- **Never hardcode API keys** in scripts or cron jobs
- Store API keys in secure files with restricted permissions (chmod 600)
- Use environment variables or secure credential files
- Example secure storage:
  ```bash
  mkdir -p ~/.config/med_auto
  chmod 700 ~/.config/med_auto
  echo "your-api-key" > ~/.config/med_auto/syncthing_api
  chmod 600 ~/.config/med_auto/syncthing_api
  ```

#### Network Access
- **Local-only by default:** Access web UIs via `http://localhost:port` when possible
- **Remote access:** For secure remote access:
  - Use SSH tunneling: `ssh -L 8384:localhost:8384 user@raspberry-pi`
  - Set up a reverse proxy (nginx/Caddy) with TLS certificates
  - Use a VPN for secure network access
  - Configure firewall rules to restrict access

#### Verification Commands
Check that services are bound correctly:
```bash
# Linux/macOS - verify listening ports and addresses
ss -tuln | grep :8384
netstat -tuln | grep :8384

# Should show 127.0.0.1:8384 for localhost-only
# Shows 0.0.0.0:8384 if exposed to network (review if intentional)
```

For detailed security configurations, refer to the setup guides in `docs/`.

## 🤝 Contributing

Feel free to add your own snippet categories or improve existing ones!

1. Create a new `.yml` file in the appropriate `match/` subdirectory
2. Follow the existing format
3. Test your snippets with Espanso
4. Sync will automatically propagate changes to all devices

## 📄 License

MIT License - See [LICENSE](LICENSE) for details

## 🔗 Resources

- [Espanso Documentation](https://espanso.org/docs/)
- [Syncthing Documentation](https://docs.syncthing.net/)
- [File Browser Documentation](https://filebrowser.org/)
