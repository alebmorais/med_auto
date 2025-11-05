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
│       ├── medical/             # Medical terminology snippets
│       │   └── common.yml
│       ├── personal/            # Personal information snippets
│       │   └── common.yml
│       ├── productivity/        # Productivity templates
│       │   └── common.yml
│       └── development/         # Development/coding snippets
│           └── common.yml
├── docs/
│   ├── setup-windows.md         # Windows installation guide
│   ├── setup-macos.md           # macOS installation guide
│   ├── setup-raspberry-pi.md   # Raspberry Pi setup guide
│   └── syncthing-setup.md      # Syncthing configuration guide
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
