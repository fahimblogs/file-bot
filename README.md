# File-Bot - Telegram File Sharing Bot

A Python-based Telegram bot for sharing and managing files efficiently through a private backup channel system.

## 🌟 Features

- **File Storage**: Store files in private Telegram channels
- **File Retrieval**: Easy file access and sharing
- **Backup System**: Automatic backup to secondary channel
- **Admin Controls**: Restricted to admin users only
- **Auto-delete**: Configurable message auto-deletion
- **Database**: JSON-based file tracking

## 📋 Requirements

- Python 3.8+
- Telegram Bot Token
- Telegram Channel IDs (for file storage)

## ⚡ Quick Start - Render Deployment

### Prerequisites
- GitHub account (you already have one!)
- Telegram Bot Token from [@BotFather](https://t.me/BotFather)
- Telegram Channel IDs for file storage and backup

### Render Setup (Step-by-Step)

#### Step 1: Create Render Account
1. Visit [render.com](https://render.com)
2. Click **"Sign up"** → Choose **"Continue with GitHub"**
3. Authorize Render to access your GitHub account
4. Accept the terms and complete signup

#### Step 2: Create a New Web Service
1. Go to your Render dashboard
2. Click **"New +"** button (top right)
3. Select **"Web Service"**
4. Under "GitHub", authorize if needed
5. Search for and select **`fahimblogs/file-bot`** repository
6. Click **"Connect"**

#### Step 3: Configure Service Settings
Fill in the following:

| Field | Value |
|-------|-------|
| **Name** | `file-bot` |
| **Region** | Choose closest to you (e.g., Singapore, Frankfurt) |
| **Branch** | `main` |
| **Runtime** | `Python 3` |
| **Build Command** | `pip install -r requirements.txt` |
| **Start Command** | `python bot.py` |
| **Instance Type** | Free (Starter Plan) |

#### Step 4: Add Environment Variables
Click **"Advanced"** → Scroll to **"Environment Variables"**

Add these variables (click "+ Add Environment Variable" for each):

```
BOT_TOKEN = your_actual_bot_token_from_botfather
FILE_CHANNEL = -1001234567890
BACKUP_CHANNEL = -1001234567890
ADMIN_IDS = 123456789,987654321
BOT_USERNAME = your_bot_username
SUPPORT_CHANNEL = https://t.me/your_channel
ADMIN_CONTACT = @your_username
DEFAULT_AUTO_DELETE = 20
```

**Important**: Replace values with your actual data:
- Get `BOT_TOKEN` from [@BotFather](https://t.me/BotFather)
- Get `FILE_CHANNEL` and `BACKUP_CHANNEL` IDs from Telegram channels
- Add your user ID to `ADMIN_IDS`

#### Step 5: Deploy
1. Click **"Create Web Service"**
2. Render will build and deploy automatically
3. Monitor the build progress in the **"Logs"** tab
4. Once it shows "✓ Deploy successful", your bot is live!

### Getting Your Telegram Channel ID

1. Create a private channel in Telegram
2. Add your bot as admin
3. Send a message: `/id`
4. The bot will respond with the channel ID
5. Format: `-100` + channel ID (e.g., `-1001234567890`)

### Getting Your Telegram Bot Token

1. Open [@BotFather](https://t.me/BotFather) in Telegram
2. Send `/start`
3. Send `/newbot`
4. Follow the prompts to create your bot
5. Copy the token provided (looks like: `123456789:ABCdefGHIjklmnoPQRstuvWXYZ`)

### Getting Your Telegram User ID

1. Open [@userinfobot](https://t.me/userinfobot) in Telegram
2. Send any message
3. It will reply with your user ID
4. Add this to `ADMIN_IDS`

## 🔧 Local Setup (Optional)

```bash
# Clone the repository
git clone https://github.com/fahimblogs/file-bot.git
cd file-bot

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Create and edit .env file
cp .env.example .env
nano .env  # or use your preferred editor

# Run the bot
python bot.py
```

## 📝 Updating Your Bot on Render

1. Make changes to your code locally
2. Commit and push to GitHub: 
   ```bash
   git add .
   git commit -m "Your message"
   git push origin main
   ```
3. Render automatically redeploys when it detects changes
4. Monitor the deployment in the **"Logs"** tab

## 🚨 Troubleshooting

| Problem | Solution |
|---------|----------|
| Bot not responding | Check if `BOT_TOKEN` is correct in environment variables |
| "Permission denied" error | Ensure bot is admin in FILE_CHANNEL and BACKUP_CHANNEL |
| Deployment fails | Check build logs in Render dashboard, ensure `requirements.txt` is valid |
| "Resource not found" | Verify `FILE_CHANNEL` and `BACKUP_CHANNEL` IDs are correct |

## 📄 License

Educational and personal use only. Redistribution allowed with proper credit.

## 👨‍💻 Original Developer

- **@JubairZ** - [Telegram](https://t.me/JubairSensei)

---

**⚠️ Security Note**: Never commit your `.env` file or BOT_TOKEN to GitHub. Always use Render's environment variables feature.
