# Quick Reference Card

## Workflow Inputs

| Input | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `kernel_branch` | string | ✅ Yes | `main` | Branch to build from |
| `enable_ksu` | boolean | ✅ Yes | `false` | Enable KernelSU support |
| `ksu_variant` | choice | ❌ No | `RKSU` | KSU variant (RKSU/xxKSU/SukiSU) |
| `enable_susfs` | boolean | ❌ No | `false` | Enable SUSFS (RKSU/SukiSU only) |
| `custom_note` | string | ❌ No | `''` | Optional build note |

## Build Matrix

| KSU | Variant | SUSFS | Result |
|-----|---------|-------|--------|
| ❌ | - | - | Stock kernel |
| ✅ | RKSU | ❌ | RKSU kernel |
| ✅ | RKSU | ✅ | RKSU + SUSFS |
| ✅ | xxKSU | ❌ | xxKSU kernel |
| ✅ | xxKSU | ✅ | xxKSU (SUSFS ignored + warning) |
| ✅ | SukiSU | ❌ | SukiSU kernel |
| ✅ | SukiSU | ✅ | SukiSU + SUSFS |

## Output Naming

```
Miku-RM6785-{VARIANT}[-SUSFS]-{TIMESTAMP}.zip
```

### Examples
- `Miku-RM6785-Stock-20241218-1430.zip`
- `Miku-RM6785-RKSU-20241218-1430.zip`
- `Miku-RM6785-RKSU-SUSFS-20241218-1430.zip`
- `Miku-RM6785-xxKSU-20241218-1430.zip`
- `Miku-RM6785-SukiSU-20241218-1430.zip`
- `Miku-RM6785-SukiSU-SUSFS-20241218-1430.zip`

## KSU Manager Links

| Variant | Manager Download | Repository |
|---------|------------------|------------|
| RKSU | [Latest Release](https://github.com/rsuntk/KernelSU/releases/latest) | [GitHub](https://github.com/rsuntk/KernelSU) |
| xxKSU | [Latest Release](https://github.com/backslashxx/KernelSU/releases/latest) | [GitHub](https://github.com/backslashxx/KernelSU) |
| SukiSU | [Latest Release](https://github.com/SukiSU-Ultra/SukiSU-Ultra/releases/latest) | [GitHub](https://github.com/SukiSU-Ultra/SukiSU-Ultra) |

## SUSFS Module

After flashing kernel with SUSFS:
- **Download**: [SUSFS Module](https://github.com/sidex15/ksu_module_susfs/releases)
- **Install**: Flash via KSU Manager
- **Required**: Only for kernels built with SUSFS enabled

## Telegram Notification Fields

### Success Message
- 📱 Device (RM6785)
- 🔢 Kernel Version
- 🏷️ Build Variant
- 💾 Image Size (MB)
- 🌿 Branch
- 📝 Commit (with link)
- ⏱️ Build Time
- 🔧 KSU Info (if enabled)
  - Variant
  - Commit (with link)
  - SUSFS status
  - Manager link
- 📌 Note (if provided)
- 📦 Download link
- 🔗 Workflow link

### Failure Message
- 📱 Device
- 🏷️ Build Variant (attempted)
- 🌿 Branch
- 📝 Commit (with link)
- 📌 Note (if provided)
- 🔗 Error logs link

## Common Branches

| Branch | Description |
|--------|-------------|
| `main` | Default/primary branch |
| `master` | Legacy default branch |
| `lineage-21.0` | LineageOS 21 |
| `lineage-20.0` | LineageOS 20 |
| `android-14` | Android 14 base |
| `android-13` | Android 13 base |

## Build Duration

| Build Type | Cold Cache | Warm Cache |
|------------|------------|------------|
| Stock | 15-20 min | 8-12 min |
| With KSU | 16-22 min | 9-13 min |
| KSU + SUSFS | 17-25 min | 10-15 min |

*Times vary based on GitHub runner load*

## Required Secrets

| Secret | Example | How to Get |
|--------|---------|------------|
| `TELEGRAM_BOT_TOKEN` | `123456:ABC-DEF...` | [@BotFather](https://t.me/botfather) |
| `TELEGRAM_CHAT_ID` | `@channel` or `-100123...` | [@userinfobot](https://t.me/userinfobot) |

## Quick Commands

### Test Workflow Locally
```bash
# Install act (GitHub Actions local runner)
curl https://raw.githubusercontent.com/nektos/act/master/install.sh | sudo bash

# Run workflow
act workflow_dispatch
```

### Check Kernel Version
```bash
cd kernel
make kernelversion
```

### View Defconfig
```bash
cat kernel/arch/arm64/configs/RM6785_defconfig
```

### Check KSU Integration
```bash
cd kernel
ls -la KernelSU/  # Should exist if KSU setup succeeded
```

## Troubleshooting Quick Fixes

| Issue | Solution |
|-------|----------|
| Build fails immediately | Check branch exists in repo |
| No Telegram message | Verify secrets are set |
| xxKSU + SUSFS warning | Normal - xxKSU doesn't support SUSFS |
| ccache not working | Delete cache in Actions settings |
| Out of space | Reduce ccache size in workflow |

## Support & Resources

- **Workflow Issues**: Open issue in workflow repo
- **Kernel Issues**: Check kernel source repo
- **KSU Issues**: Visit respective KSU repository
- **SUSFS Issues**: Visit [SUSFS Module repo](https://github.com/sidex15/ksu_module_susfs)

---

**Last Updated**: December 2024 (v2.0)
