# Satisfactory Server Connection Guide

## 🏠 LAN (Local Network) Connection

### Recommended Method - Direct Connection:

1. **Launch Satisfactory** on your gaming PC/device
2. **From the Main Menu**, select **"Join Game"**
3. At the bottom, click **"Add Server"** or enter directly:
   - **Server Address**: `192.168.4.50:7777`
4. Click **"Confirm"** and **"Join"**
5. **Enter Password** when prompted: `kevin`

### Alternative - Console Connection:
- Press **`` ` ``** or **`~`** to open the console
- Type: `open 192.168.4.50:7777`
- Press Enter

### Server Manager Method:
✅ **Server Manager now works!** (after disabling MULTIHOME)
- Go to **"Server Manager"** → **"Add Server"**
- Enter: `192.168.4.50:7777`
- Use this for server administration, viewing status, and managing saves

✅ **This works for any device on your home network (192.168.4.x subnet)**

---

## 🌐 WAN (Internet/Remote) Connection

To allow friends or remote connections from outside your home network, you need to configure port forwarding.

### Step 1: Find Your Public IP Address

**Option A - Use a Website:**
- Visit: https://whatismyipaddress.com/
- Note your IPv4 address

**Option B - Command Line:**
```powershell
Invoke-RestMethod -Uri https://api.ipify.org
```

### Step 2: Configure Port Forwarding on Your Router

You need to forward these ports to your server machine (192.168.4.50):

| Port | Protocol | Description |
|------|----------|-------------|
| 7777 | **TCP + UDP** | Game Port & HTTPS API (Primary) |
| 15000 | UDP | Query Port |
| 15777 | UDP | Beacon Port |

#### Generic Router Configuration Steps:

1. **Access Router Admin Panel**
   - Open browser and go to: `http://192.168.4.1`
   - Login with your router credentials

2. **Find Port Forwarding Section**
   - Look for: "Port Forwarding", "Virtual Server", "NAT", or "Applications"

3. **Create Port Forwarding Rules**

   **Rule 1 - Game Port (UDP):**
   - Service Name: `Satisfactory-Game`
   - External Port: `7777`
   - Internal Port: `7777`
   - Internal IP: `192.168.4.50`
   - Protocol: `UDP`
   
   **Rule 2 - HTTPS API (TCP):**
   - Service Name: `Satisfactory-API`
   - External Port: `7777`
   - Internal Port: `7777`
   - Internal IP: `192.168.4.50`
   - Protocol: `TCP`
   
   **Rule 3 - Query Port:**
   - Service Name: `Satisfactory-Query`
   - External Port: `15000`
   - Internal Port: `15000`
   - Internal IP: `192.168.4.50`
   - Protocol: `UDP`
   
   **Rule 4 - Beacon Port:**
   - Service Name: `Satisfactory-Beacon`
   - External Port: `15777`
   - Internal Port: `15777`
   - Internal IP: `192.168.4.50`
   - Protocol: `UDP`

4. **Save and Apply** the changes

### Step 3: Configure Windows Firewall (If Needed)

The Docker container should handle this, but if connections fail, add firewall rules:

```powershell
# Run PowerShell as Administrator
New-NetFirewallRule -DisplayName "Satisfactory Server UDP" -Direction Inbound -LocalPort 7777,15000,15777 -Protocol UDP -Action Allow
New-NetFirewallRule -DisplayName "Satisfactory Server TCP" -Direction Inbound -LocalPort 7777 -Protocol TCP -Action Allow
```

### Step 4: Connect from Outside Your Network

**Share these connection details with friends:**
- **Server Address**: `74.137.165.140:7777`
- **Server Name**: nokevins
- **Password**: kevin
- **Password**: kevin

**Connection Steps for Remote Players:**
1. Launch Satisfactory
2. Go to **"Join Game"** (NOT Server Manager)
3. Click **"Add Server"**
4. Enter: `74.137.165.140:7777`
5. Click **"Join"**

**Alternative - Console Method:**
- Open console with **`` ` ``** or **`~`**
- Type: `open 74.137.165.140:7777`

---

## 🔍 Troubleshooting

### LAN Connection Issues:
- ✅ Verify server is running: `docker ps | findstr satisfactory`
- ✅ Check you're on the same network (192.168.4.x)
- ✅ Ping the server: `ping 192.168.4.50`
- ✅ Verify ports are listening: `netstat -an | findstr 7777`

### WAN Connection Issues:
- ✅ Verify port forwarding is configured correctly
- ✅ Test your public IP: https://www.yougetsignal.com/tools/open-ports/
  - Enter your public IP and port 7777 to test if it's open
- ✅ Check your ISP doesn't block UDP ports
- ✅ Ensure router firewall allows UDP traffic
- ✅ Verify server is running and healthy: `docker ps`

### Dynamic IP Issues:
If your public IP changes frequently:
- Consider using a Dynamic DNS service (DuckDNS, No-IP, etc.)
- Or check your public IP before each session

### Server Manager Shows Blank/Empty Screens:
- ✅ This is normal - the server uses a self-signed HTTPS certificate
- ✅ Use the **"Join Game"** method instead of Server Manager
- ✅ Server Manager is optional; direct connection works better

### "Network Error: Encryption Token Missing":
- ✅ **Solution**: Set `MULTIHOME=` (empty) in docker-compose.yml
- ✅ Ensure both TCP and UDP port 7777 are forwarded
- ✅ Restart the container: `docker-compose down && docker-compose up -d`
- ✅ Wait 1-2 minutes for server to fully start

---

## 📊 Server Information

- **Version**: Satisfactory 1.1.0 (Build 463028)
- **Server Name**: nokevins
- **Max Players**: 8
- **Game Port**: 7777/UDP + TCP
- **Query Port**: 15000/UDP
- **Beacon Port**: 15777/UDP
- **Container Name**: satisfactory-server
- **Client Password**: kevin (required to join)
- **Admin Password**: nokevins (for admin commands)
- **Important**: `MULTIHOME=` must be empty in docker-compose.yml

---

## 🛠️ Useful Commands

### Check Server Status:
```bash
docker ps | findstr satisfactory
```

### View Server Logs:
```bash
docker logs satisfactory-server --tail 50
```

### Restart Server:
```bash
docker restart satisfactory-server
```

### Stop Server:
```bash
docker stop satisfactory-server
```

### Start Server:
```bash
docker start satisfactory-server
```

---

## 🔐 Security Notes

⚠️ **Important Security Considerations:**

1. **Server Password**: ✅ **ENABLED** - Password required to join
   - **Client Password**: `kevin` (set in Server Manager)
   - **Admin Password**: `nokevins` (set in Server Manager)
   - Players must enter the client password to join
   - Admins use the admin password for console commands
   - **To Change Passwords**: Use Server Manager settings or edit ServerSettings.ini
2. **Port Exposure**: Both TCP + UDP on 7777, UDP on 15000 & 15777
3. **Firewall**: Keep Windows Firewall enabled
4. **Updates**: Regularly update the server container
5. **Backups**: The server auto-saves to `/config/saved/` (mapped to `./saved/`)

---

## 📝 Quick Reference

| Connection Type | Address Format | Example |
|----------------|----------------|---------|
| **LAN (Same Network)** | `192.168.4.50:7777` | Connect from any device on your WiFi |
| **WAN (Internet)** | `YOUR_PUBLIC_IP:7777` | Friends connecting from outside |
| **Localhost (Server PC)** | `localhost:7777` or `127.0.0.1:7777` | Playing on the server machine |

---

**Need help?** Check Docker logs or server logs in `gamefiles/FactoryGame/Saved/Logs/FactoryGame.log`
