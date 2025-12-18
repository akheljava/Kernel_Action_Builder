# Quick Start Guide

## Setup in 5 Minutes

### Step 1: Get Telegram Bot Token

1. Open Telegram and search for [@BotFather](https://t.me/botfather)
2. Send `/newbot` command
3. Follow instructions to create your bot
4. Copy the token (looks like: `1234567890:ABCdefGHIjklMNOpqrsTUVwxyz`)

### Step 2: Get Chat ID

**For Private Chat:**
1. Search for [@userinfobot](https://t.me/userinfobot) on Telegram
2. Start chat, it will show your user ID

**For Channel:**
1. Add your bot to the channel as admin
2. Forward any message from channel to [@userinfobot](https://t.me/userinfobot)
3. Copy the chat ID (starts with `-100`)

**For Group:**
1. Add your bot to the group
2. Add [@userinfobot](https://t.me/userinfobot) to group
3. Copy the chat ID shown

### Step 3: Configure GitHub Secrets

1. Go to your repository on GitHub
2. Click **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret**
4. Add these secrets:
   - Name: `TELEGRAM_BOT_TOKEN`, Value: Your bot token
   - Name: `TELEGRAM_CHAT_ID`, Value: Your chat/channel ID

### Step 4: Run Your First Build

1. Go to **Actions** tab
2. Click **Build RM6785 Kernel**
3. Click **Run workflow** button
4. Select options:
   - Kernel Branch: `main` (or your default branch)
   - Enable KernelSU: Leave **unchecked** for first test
   - Custom note: "First test build"
5. Click **Run workflow**

### Step 5: Monitor Build

1. Wait for workflow to start (~30 seconds)
2. Click on the running workflow to see logs
3. Build takes 15-25 minutes (first time)
4. Check Telegram for notification when done!

## Your First KSU Build

After successful test build, try KSU:

1. **Run workflow** again
2. Select options:
   - **Kernel Branch**: `main`
   - **Enable KernelSU**: Check this ✅
   - **KernelSU Variant**: `RKSU`
   - **Enable SUSFS**: Check this ✅
   - **Custom note**: "RKSU with SUSFS"
3. **Run workflow**

The notification will include a direct link to download the RKSU Manager!

Download the built kernel from workflow artifacts!

## Flashing

1. Download the `.zip` file from artifacts
2. Reboot to recovery (TWRP/OrangeFox)
3. Flash the zip
4. Reboot
5. Install KSU Manager app

## Need Help?

- Workflow not starting? Check Actions are enabled in repository settings
- Build failing? Check workflow logs for errors
- No Telegram message? Verify bot token and chat ID
- Kernel not booting? Start with stock build (no KSU) first

## Next Steps

- Enable schedule builds (cron)
- Customize AnyKernel3 banner
- Add more build variants
- Setup automatic releases

Happy building! 🚀
