# 🚀 cPanel 24/7 Deployment Guide for File-Bot

## 📋 Prerequisites

- cPanel hosting with SSH access
- Python 3.8+ installed on server
- Your configured `bot.py` with credentials
- `requirements.txt` with dependencies
- Paid cPanel account (for 24/7 running processes)

---

## 🔧 Step-by-Step cPanel Setup

### Step 1: Connect to Your Server via SSH

**Using Terminal/Command Prompt:**

```bash
ssh username@your-domain.com
# Or
ssh username@your-server-ip
```

**Enter your cPanel username and password when prompted**

Or use an SSH client like:
- **PuTTY** (Windows) - Download from putty.org
- **Terminal** (Mac/Linux) - Built-in

---

### Step 2: Create Project Directory

```bash
# Go to home directory
cd ~

# Create folder for bot
mkdir telegram-bot
cd telegram-bot

# Create virtual environment
python3 -m venv venv

# Activate virtual environment
source venv/bin/activate
```

---

### Step 3: Upload Your Bot Files

**Option A: Using Git (Recommended)**

```bash
git clone https://github.com/fahimblogs/file-bot.git
cd file-bot
```

**Option B: Upload via cPanel File Manager**

1. Login to cPanel
2. Go to **File Manager**
3. Navigate to `public_html` or your directory
4. Upload `bot.py`, `requirements.txt`, and other files

**Option C: Using SCP (Secure Copy)**

```bash
scp -r bot.py requirements.txt username@your-domain.com:~/telegram-bot/
```

---

### Step 4: Install Dependencies

```bash
cd ~/telegram-bot

# Make sure venv is activated
source venv/bin/activate

# Install packages
pip install -r requirements.txt

# Verify installation
python3 -c "import aiogram; print('✓ aiogram installed')"
```

---

### Step 5: Create Startup Script

Create a file named `start_bot.sh`:

```bash
nano start_bot.sh
```

Paste this content:

```bash
#!/bin/bash

# Bot startup script for cPanel
PATH_TO_BOT="/home/username/telegram-bot"
PYTHON_VENV="$PATH_TO_BOT/venv/bin/python3"
LOG_FILE="$PATH_TO_BOT/bot.log"

# Activate virtual environment and run bot
cd $PATH_TO_BOT
$PYTHON_VENV bot.py >> $LOG_FILE 2>&1
```

**Replace `username` with your actual cPanel username**

Save with: `Ctrl+X` → `Y` → `Enter`

Make it executable:

```bash
chmod +x start_bot.sh
```

---

### Step 6: Setup Cron Job for Auto-Restart (24/7)

In cPanel:

1. Go to **Cron Jobs** (search in cPanel home)
2. Click **Add New Cron Job**
3. Fill in the fields:
   - **Common Settings**: Every 5 minutes (or custom)
   - **Command**: 
   ```
   /home/username/telegram-bot/check_bot.sh
   ```

**Create the check script:**

```bash
nano check_bot.sh
```

Paste:

```bash
#!/bin/bash

PATH_TO_BOT="/home/username/telegram-bot"
PYTHON_BIN="$PATH_TO_BOT/venv/bin/python3"
PYTHON_SCRIPT="$PATH_TO_BOT/bot.py"
LOG_FILE="$PATH_TO_BOT/bot.log"
PID_FILE="$PATH_TO_BOT/bot.pid"

# Check if process is running
if [ -f "$PID_FILE" ]; then
    PID=$(cat "$PID_FILE")
    if ps -p $PID > /dev/null; then
        exit 0  # Process running, do nothing
    fi
fi

# Start bot if not running
cd "$PATH_TO_BOT"
$PYTHON_BIN "$PYTHON_SCRIPT" > "$LOG_FILE" 2>&1 &
echo $! > "$PID_FILE"
```

Make executable:

```bash
chmod +x check_bot.sh
```

---

### Step 7: Better Approach - Using Systemd Service

**Create a service file** (requires root/sudo access):

```bash
sudo nano /etc/systemd/system/telegram-bot.service
```

Paste:

```ini
[Unit]
Description=Telegram File Bot
After=network.target

[Service]
Type=simple
User=username
WorkingDirectory=/home/username/telegram-bot
ExecStart=/home/username/telegram-bot/venv/bin/python3 /home/username/telegram-bot/bot.py
Restart=always
RestartSec=10
StandardOutput=append:/home/username/telegram-bot/bot.log
StandardError=append:/home/username/telegram-bot/bot.log

[Install]
WantedBy=multi-user.target
```

Enable and start:

```bash
sudo systemctl daemon-reload
sudo systemctl enable telegram-bot
sudo systemctl start telegram-bot
sudo systemctl status telegram-bot
```

---

### Step 8: Monitor Bot Status

**Check if bot is running:**

```bash
ps aux | grep bot.py
```

**View logs in real-time:**

```bash
tail -f ~/telegram-bot/bot.log
```

**View last 50 lines:**

```bash
tail -50 ~/telegram-bot/bot.log
```

---

### Step 9: Update Environment Variables (if needed)

If your bot uses `.env` file:

```bash
nano ~/telegram-bot/.env
```

Add:

```
BOT_TOKEN=8609897017:AAGtiQIHGFeo7mH34zmSJuJu98EMWkq6QaM
FILE_CHANNEL=-1003859240003
BACKUP_CHANNEL=-1003859240003
ADMIN_IDS=5520625960
BOT_USERNAME=filesbyfahimbot
SUPPORT_CHANNEL=https://t.me/filesharebyfahim
ADMIN_CONTACT=@fahim582
DEFAULT_AUTO_DELETE=20
```

Update `bot.py` to use `.env` if not already done:

```python
from dotenv import load_dotenv

load_dotenv()

BOT_TOKEN = os.getenv("BOT_TOKEN", "8609897017:AAGtiQIHGFeo7mH34zmSJuJu98EMWkq6QaM")
FILE_CHANNEL = int(os.getenv("FILE_CHANNEL", "-1003859240003"))
# ... etc
```

---

## 📊 Comparison: Render vs cPanel

| Feature | Render | cPanel |
|---------|--------|--------|
| **Uptime** | 99.9% (Paid) / 15min sleep (Free) | 99.9% (24/7) |
| **Cost** | Free tier available | Paid (usually $5-15/month) |
| **Setup Time** | 5 minutes | 30 minutes |
| **Database** | Limited | Full control |
| **SSH Access** | No | Yes |
| **Cron Jobs** | No | Yes |
| **Resources** | Limited | More resources |

---

## ✅ Testing Your Setup

1. **SSH into server and restart bot:**
   ```bash
   systemctl restart telegram-bot
   ```

2. **Send test message to bot**

3. **Check logs for errors:**
   ```bash
   tail -f ~/telegram-bot/bot.log
   ```

4. **Verify process:**
   ```bash
   ps aux | grep bot.py
   ```

---

## 🚨 Troubleshooting

### Bot Not Starting

```bash
# Check for errors
~/telegram-bot/venv/bin/python3 ~/telegram-bot/bot.py

# Check service status
systemctl status telegram-bot

# View detailed logs
journalctl -u telegram-bot -n 50
```

### Port/Connection Issues

```bash
# Check if port is open
netstat -tuln | grep 8000

# Check firewall
sudo firewall-cmd --list-all
```

### High CPU Usage

```bash
# Monitor processes
top -p $(pgrep -f "bot.py")

# Kill and restart
pkill -f "bot.py"
systemctl start telegram-bot
```

### Permission Denied

```bash
# Fix permissions
chmod 755 ~/telegram-bot/start_bot.sh
chmod 755 ~/telegram-bot/check_bot.sh
```

---

## 🔄 Updating Bot Code

1. **SSH into server:**
   ```bash
   cd ~/telegram-bot
   git pull origin main
   ```

2. **Restart bot:**
   ```bash
   systemctl restart telegram-bot
   ```

3. **Check logs:**
   ```bash
   tail -f ~/telegram-bot/bot.log
   ```

---

## 📝 Backup & Restore

**Backup database:**

```bash
cp ~/telegram-bot/database.json ~/telegram-bot/backups/database_$(date +%Y%m%d_%H%M%S).json
```

**Automatic daily backup (via Cron):**

```bash
# Add to Cron Job:
0 2 * * * cp /home/username/telegram-bot/database.json /home/username/telegram-bot/backups/database_$(date +\%Y\%m\%d).json
```

---

## 🎯 Summary

✅ Bot running 24/7 on cPanel  
✅ Auto-restarts on crash  
✅ Log monitoring enabled  
✅ Environment variables configured  
�� Database persisted  
✅ Easy updates via Git

Your bot should now be running continuously on your cPanel server! 🚀
