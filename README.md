# Satisfactory Dedicated Server

Docker-based Satisfactory dedicated server setup for hosting multiplayer games.

## 🚀 Quick Start

1. **Clone this repository**
   ```bash
   git clone https://github.com/theOranguzang/satisfactory-server.git
   cd satisfactory-server
   ```

2. **Start the server**
   ```bash
   docker-compose up -d
   ```

3. **Connect to the server**
   - **LAN**: `192.168.4.50:7777`
   - **WAN**: `74.137.165.140:7777`
   - **Password**: `kevin`

For detailed connection instructions, see [QUICKSTART.md](QUICKSTART.md).

## 📋 Server Information

- **Server Name**: nokevins
- **Max Players**: 8
- **Version**: 1.1.0 (Build 463028)
- **Client Password**: kevin
- **Admin Password**: nokevins

## 🔧 Configuration

### Docker Setup

The server runs using the `wolveix/satisfactory-server:latest` Docker image with the following configuration:

- **Ports**:
  - 7777/tcp+udp - Game server & HTTPS API
  - 15000/udp - Query port
  - 15777/udp - Beacon port
- **Resources**: 4 CPU cores, 8GB RAM
- **Restart Policy**: unless-stopped

### Important Configuration Notes

**MULTIHOME Setting**: Must be empty (`MULTIHOME=`) for proper Docker networking on Windows. Setting this to any IP address (including `::`) will cause authentication failures.

**Timeout Configuration**: Modified `config/gamefiles/FactoryGame/Saved/Config/LinuxServer/Engine.ini` to increase connection timeouts:

```ini
[/Script/OnlineSubsystemUtils.IpNetDriver]
InitialConnectTimeout=180.0
ConnectionTimeout=180.0

[ReliableMessaging]
HandshakeTimeout=180.0
```

These extended timeouts (180 seconds) are necessary for initial world synchronization, which can take 45-60 seconds.

## 🌐 Network Requirements

### Port Forwarding

Configure your router to forward these ports to `192.168.4.50`:

| Port | Protocol | Purpose |
|------|----------|---------|
| 7777 | TCP+UDP  | Game server & HTTPS API |
| 15000 | UDP     | Query port |
| 15777 | UDP     | Beacon port |

### Windows Firewall

The following firewall rules are required:

```powershell
# TCP Rule
New-NetFirewallRule -DisplayName "Satisfactory Server TCP" -Direction Inbound -Protocol TCP -LocalPort 7777 -Action Allow

# UDP Rules
New-NetFirewallRule -DisplayName "Satisfactory Server UDP" -Direction Inbound -Protocol UDP -LocalPort 7777,15000,15777 -Action Allow
```

## 🔐 Server Management

Access the Satisfactory Server Manager at `https://192.168.4.50:7777` (note: will show certificate warning for self-signed cert).

**Authentication**:
- Initial claiming password: nokevins
- Admin password: nokevins

## 🐛 Troubleshooting

### Connection Issues

1. **"Failed to connect to server API"**
   - Verify both TCP and UDP port 7777 are open
   - Accept the self-signed HTTPS certificate in Server Manager
   - Check Windows Firewall rules

2. **"Encryption token missing"**
   - Ensure `MULTIHOME=` is empty in docker-compose.yml
   - Restart the container: `docker-compose restart`

3. **"Connection timeout during world loading"**
   - This can occur on initial connections with large worlds
   - Default timeout is 30 seconds but world sync may take 45-60 seconds
   - Engine.ini has been modified with 180-second timeouts
   - If issues persist, consider wired Ethernet connection

### Logs

View server logs:
```bash
docker logs -f satisfactory-server
```

### Restart Server

```bash
docker-compose restart
```

## 📚 Documentation

- [CONNECTION_GUIDE.md](CONNECTION_GUIDE.md) - Comprehensive connection and troubleshooting guide
- [QUICKSTART.md](QUICKSTART.md) - Minimalist connection guide for friends

## 🛠️ Tech Stack

- **Container**: Docker Desktop on Windows (WSL2/Hyper-V)
- **Image**: wolveix/satisfactory-server:latest
- **Networking**: Bridge mode with port mappings
- **Storage**: Volume mount `./config:/config` for persistent data

## ⚠️ Known Issues

- **Initial Connection Timeouts**: Some clients may experience timeouts during the initial world loading phase (45-60 seconds). This appears to be related to Docker bridge networking overhead on Windows. The Engine.ini timeout settings help but may not completely eliminate the issue.
- **Server Manager Certificate**: The HTTPS API uses a self-signed certificate which will trigger browser warnings. This is normal and safe to accept.

## 📝 Version Control

This repository tracks:
- Docker Compose configuration
- Documentation files
- Connection guides

The following directories are excluded (via .gitignore):
- `config/` - Server data and saves
- `gamefiles/` - Server binaries
- `backups/` - Save backups
- `logs/` - Server logs
- `saved/` - Additional save data

## 📄 License

This is a personal server setup. Satisfactory is developed by Coffee Stain Studios.
