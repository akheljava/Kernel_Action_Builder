# RM6785 Kernel Build Workflow

Automated GitHub Actions workflow for building RM6785 (Realme 6/6i/6s/7/Narzo 20 Pro) kernel with optional KernelSU support.

## Features

- ✅ Multiple KernelSU variants support (RKSU, xxKSU, SukiSU)
- ✅ Optional SUSFS support
- ✅ Stock kernel build (no KSU)
- ✅ Automated Telegram notifications
- ✅ ccache for faster subsequent builds
- ✅ AnyKernel3 packaging
- ✅ Artifact uploads

## Supported Devices

- Realme 6 (RMX2001)
- Realme 6i (RMX2002)
- Realme 6s (RMX2002)
- Realme 7 (RMX2155)
- Realme Narzo 20 Pro (RMX2161)

## Setup

### 1. Fork/Create Repository

Create a new repository or fork this one for your build workflow.

### 2. Configure Secrets

Go to your repository **Settings → Secrets and variables → Actions** and add:

- `TELEGRAM_BOT_TOKEN` - Your Telegram bot token (get from [@BotFather](https://t.me/botfather))
- `TELEGRAM_CHAT_ID` - Your Telegram chat ID or channel username (e.g., `@your_channel` or `-1001234567890`)

### 3. Customize (Optional)

Edit `.github/workflows/build-kernel.yml` if you need to change:

- Kernel repository/branch
- AnyKernel3 repository/branch
- Defconfig name
- Clang toolchain

## Usage

### Manual Build

1. Go to **Actions** tab in your repository
2. Select **Build RM6785 Kernel** workflow
3. Click **Run workflow**
4. Configure options:
   - **Kernel Branch**: Enter the branch name (e.g., `main`, `master`, `lineage-21.0`)
   - **Enable KernelSU**: Check to enable KSU support
   - **KernelSU Variant**: Choose RKSU/xxKSU/SukiSU (only if KSU enabled)
   - **Enable SUSFS**: Check for SUSFS support (only works with RKSU/SukiSU)
   - **Custom build note**: Optional notes (only shown if provided)

### Important Notes

- **SUSFS requires KSU**: SUSFS can only be enabled when KSU is also enabled
- **xxKSU doesn't support SUSFS**: If you select xxKSU, SUSFS will be ignored
- **RKSU and SukiSU support SUSFS**: Both variants have full SUSFS support

### Build Naming Convention

Kernels are named automatically based on your configuration:

- **Stock**: `Miku-RM6785-Stock-YYYYMMDD-HHMM.zip`
- **KSU without SUSFS**: `Miku-RM6785-{VARIANT}-YYYYMMDD-HHMM.zip`
- **KSU with SUSFS**: `Miku-RM6785-{VARIANT}-SUSFS-YYYYMMDD-HHMM.zip`

Examples:
- `Miku-RM6785-Stock-20241218-1430.zip`
- `Miku-RM6785-RKSU-20241218-1430.zip`
- `Miku-RM6785-RKSU-SUSFS-20241218-1430.zip`
- `Miku-RM6785-SukiSU-SUSFS-20241218-1430.zip`
- `Miku-RM6785-xxKSU-20241218-1430.zip`

### KernelSU Variants

| Variant | Description | SUSFS Support | Manager Download |
|---------|-------------|---------------|------------------|
| **Stock** | Stock kernel without KSU | ❌ | - |
| **RKSU** | Rissu's KernelSU | ✅ Yes | [Download](https://github.com/rsuntk/KernelSU/releases/latest) |
| **xxKSU** | backslashxx's KernelSU | ❌ No | [Download](https://github.com/backslashxx/KernelSU/releases/latest) |
| **SukiSU** | SukiSU-Ultra | ✅ Yes | [Download](https://github.com/SukiSU-Ultra/SukiSU-Ultra/releases/latest) |

**Note**: Manager links are automatically included in Telegram notifications for each build.

### SUSFS Configuration

When SUSFS is enabled, the following configs are automatically added:

```
CONFIG_KSU_SUSFS=y
CONFIG_KSU_SUSFS_HAS_MAGIC_MOUNT=y
CONFIG_KSU_SUSFS_SUS_PATH=y
CONFIG_KSU_SUSFS_SUS_MOUNT=y
CONFIG_KSU_SUSFS_AUTO_ADD_SUS_KSU_DEFAULT_MOUNT=y
CONFIG_KSU_SUSFS_AUTO_ADD_SUS_BIND_MOUNT=y
CONFIG_KSU_SUSFS_SUS_KSTAT=y
CONFIG_KSU_SUSFS_TRY_UMOUNT=y
CONFIG_KSU_SUSFS_AUTO_ADD_TRY_UMOUNT_FOR_BIND_MOUNT=y
CONFIG_KSU_SUSFS_SPOOF_UNAME=y
CONFIG_KSU_SUSFS_ENABLE_LOG=y
CONFIG_KSU_SUSFS_HIDE_KSU_SUSFS_SYMBOLS=y
CONFIG_KSU_SUSFS_SPOOF_CMDLINE_OR_BOOTCONFIG=y
CONFIG_KSU_SUSFS_OPEN_REDIRECT=y
# CONFIG_KSU_SUSFS_SUS_MAP is not set
```

## Telegram Notifications

The workflow sends rich notifications to your Telegram channel/chat:

**On Success:**
- ✅ Build status with emoji
- Device information
- Kernel version
- Build variant (shows KSU type + SUSFS if enabled)
- Image size in MB
- Branch name
- Commit hash with link
- Build duration (minutes and seconds)
- KSU commit info (for KSU builds)
- Direct manager download link (hyperlinked, not full URL)
- Custom note (only if provided)
- Download and workflow links

**On Failure:**
- ❌ Build status
- Device info
- Build variant attempted
- Branch and commit
- Custom note (only if provided)
- Direct link to error logs

**Note Display Logic:**
- Notes are only shown in notifications when provided
- Empty notes are automatically hidden
- Keeps notifications clean and relevant

## Output

Compiled kernels are available as:

1. **GitHub Artifacts**: In the workflow run (retained for 30 days)
2. **Naming format**: 
   - Stock: `Miku-RM6785-Stock-{TIMESTAMP}.zip`
   - KSU: `Miku-RM6785-{VARIANT}-{TIMESTAMP}.zip`
   - KSU+SUSFS: `Miku-RM6785-{VARIANT}-SUSFS-{TIMESTAMP}.zip`

Examples:
- `Miku-RM6785-Stock-20241218-1430.zip`
- `Miku-RM6785-RKSU-20241218-1430.zip`
- `Miku-RM6785-RKSU-SUSFS-20241218-1430.zip`
- `Miku-RM6785-xxKSU-20241218-1430.zip`
- `Miku-RM6785-SukiSU-SUSFS-20241218-1430.zip`

## Build Time

Typical build times:
- First build: ~15-25 minutes
- Cached builds: ~8-15 minutes

## Troubleshooting

### Build Fails

1. Check workflow logs in Actions tab
2. Verify kernel source is accessible
3. Ensure defconfig exists in kernel repo
4. Check if KSU setup script is accessible

### No Telegram Notification

1. Verify bot token is correct
2. Check chat ID format (should include `-` for supergroups)
3. Ensure bot is added to the channel/group
4. Bot needs admin rights in channels

### SUSFS Not Working

1. SUSFS only works with RKSU and SukiSU
2. xxKSU doesn't support SUSFS
3. Install [SUSFS module](https://github.com/sidex15/ksu_module_susfs/releases) after flashing

## Credits

- [KernelSU](https://github.com/tiann/KernelSU) by tiann
- [RKSU](https://github.com/rsuntk/KernelSU) by rsuntk
- [xxKSU](https://github.com/backslashxx/KernelSU) by backslashxx
- [SukiSU-Ultra](https://github.com/SukiSU-Ultra/SukiSU-Ultra) by SukiSU team
- [SUSFS](https://github.com/sidex15/ksu_module_susfs) by sidex15
- [AnyKernel3](https://github.com/osm0sis/AnyKernel3) by osm0sis

## License

This workflow is provided as-is. Kernel licensing depends on your kernel source.

## Support

For issues related to:
- **Workflow**: Open an issue in this repository
- **Kernel**: Check kernel source repository
- **KernelSU**: Visit respective KSU variant repository
