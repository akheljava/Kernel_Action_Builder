# Example Configuration

This file shows how to customize the workflow for your needs.

## New Workflow Inputs (v2.0)

The workflow now uses improved input validation:

### Kernel Branch
```yaml
kernel_branch:
  description: 'Kernel Branch'
  required: true
  type: string
  default: 'main'
```
**Usage**: Enter any valid branch from your kernel repo (e.g., `main`, `master`, `lineage-21.0`)

### Enable KernelSU
```yaml
enable_ksu:
  description: 'Enable KernelSU'
  required: true
  type: boolean
  default: false
```
**Usage**: Check to enable KSU support. When unchecked, builds stock kernel.

### KernelSU Variant
```yaml
ksu_variant:
  description: 'KernelSU Variant (only if KSU enabled)'
  required: false
  type: choice
  options:
    - 'RKSU'
    - 'xxKSU'
    - 'SukiSU'
  default: 'RKSU'
```
**Usage**: Only relevant when KSU is enabled. Choose your preferred variant.

### Enable SUSFS
```yaml
enable_susfs:
  description: 'Enable SUSFS (only works with RKSU/SukiSU)'
  required: false
  type: boolean
  default: false
```
**Usage**: Only works when KSU is enabled AND variant is RKSU or SukiSU.

## Conditional Logic Flow

### Stock Build (No KSU)
```
enable_ksu = false
↓
Skips all KSU setup steps
↓
Builds stock kernel
↓
Output: Miku-RM6785-Stock-TIMESTAMP.zip
```

### KSU Build without SUSFS
```
enable_ksu = true
ksu_variant = RKSU
enable_susfs = false
↓
Runs RKSU setup (main branch)
↓
Adds CONFIG_KSU=y
↓
Output: Miku-RM6785-RKSU-TIMESTAMP.zip
```

### KSU Build with SUSFS
```
enable_ksu = true
ksu_variant = RKSU
enable_susfs = true
↓
Runs RKSU setup (susfs-rksu-master branch)
↓
Adds CONFIG_KSU=y + all SUSFS configs
↓
Output: Miku-RM6785-RKSU-SUSFS-TIMESTAMP.zip
```

### xxKSU with SUSFS (Warning Case)
```
enable_ksu = true
ksu_variant = xxKSU
enable_susfs = true
↓
Runs xxKSU setup
↓
Shows warning: "xxKSU does not support SUSFS"
↓
Ignores SUSFS flag
↓
Output: Miku-RM6785-xxKSU-TIMESTAMP.zip
```

## Workflow Variables

Edit `.github/workflows/build-kernel.yml` to customize:

### Kernel Source
```yaml
KERNEL_REPO: "https://github.com/elohim-etz/android_kernel_realme_mt6785.git"
# Note: Branch is now selected during workflow run, not hardcoded here
```

### Defconfig
```yaml
DEFCONFIG: "RM6785_defconfig"  # Your kernel's defconfig name
```

### Toolchain
```yaml
CLANG_REPO: "https://gitlab.com/crdroidandroid/android_prebuilts_clang_host_linux-x86_clang-r547379.git"
```

Alternative clang sources:
- Proton Clang: `https://github.com/kdrag0n/proton-clang`
- AOSP Clang: Download from Android source
- Zyc Clang: `https://github.com/ZyCromerZ/Clang`

### AnyKernel3
```yaml
ANYKERNEL_REPO: "https://github.com/elohim-etz/AK3.git"
ANYKERNEL_BRANCH: "mikuchan"
```

### Build Info
```yaml
KBUILD_BUILD_USER: "elohim-etz"  # Your name
KBUILD_BUILD_HOST: "GitHub-Actions"  # Build host identifier
```

## KernelSU Repositories

The workflow uses these KSU sources:

### RKSU (Rissu's KernelSU)
- Main: `https://github.com/rsuntk/KernelSU` (branch: `main`)
- SUSFS: `https://github.com/rsuntk/KernelSU` (branch: `susfs-rksu-master`)

### xxKSU (backslashxx's KernelSU)
- Main: `https://github.com/backslashxx/KernelSU` (branch: `master`)
- Note: No SUSFS support

### SukiSU (SukiSU-Ultra)
- Main: `https://github.com/SukiSU-Ultra/SukiSU-Ultra` (branch: `builtin`)
- SUSFS: Built-in support

## Telegram Message Customization

Edit the Telegram notification steps to customize messages:

### Success Message Variables
- `$KERNEL_VERSION` - Kernel version from Makefile
- `$KSU_VARIANT` - Selected KSU variant (RKSU/xxKSU/SukiSU/Stock)
- `$SUSFS_ENABLED` - Boolean flag for SUSFS status
- `$IMAGE_SIZE_MB` - Compiled kernel size in MB
- `$BUILD_DURATION` - Total build time in seconds
- `$KSU_COMMIT` - Short KSU commit hash (7 chars)
- `$KSU_COMMIT_URL` - Full URL to KSU commit
- `$KSU_MANAGER_URL` - URL to manager releases
- `$KERNEL_BRANCH` - Branch that was built
- `$commit_hash` - Kernel commit hash

### Conditional Display Logic

**Show KSU info only when KSU is enabled:**
```bash
if [ "${{ github.event.inputs.enable_ksu }}" == "true" ]; then
  MESSAGE+="*KSU Info:* ..."
fi
```

**Show note only when provided:**
```bash
if [ -n "$note" ]; then
  MESSAGE+="*Note:* ${note}"
fi
```

**Show SUSFS status:**
```bash
if [ "$SUSFS_ENABLED" == "true" ]; then
  MESSAGE+="SUSFS: ✅ Enabled"
else
  MESSAGE+="SUSFS: ❌ Disabled"
fi
```

### Add Custom Fields

Add more info to Telegram messages:

```bash
MESSAGE+="*Custom Field:* Your value here%0A"
MESSAGE+="*Build Number:* ${{ github.run_number }}%0A"
MESSAGE+="*Workflow:* ${{ github.workflow }}%0A"
```

### Manager Link Format

Each variant gets its manager link:
```bash
case "${{ github.event.inputs.ksu_variant }}" in
  "RKSU")
    MESSAGE+="└ Manager: [Download RKSU Manager](${KSU_MANAGER_URL})%0A"
    ;;
  "xxKSU")
    MESSAGE+="└ Manager: [Download xxKSU Manager](${KSU_MANAGER_URL})%0A"
    ;;
  "SukiSU")
    MESSAGE+="└ Manager: [Download SukiSU Manager](${KSU_MANAGER_URL})%0A"
    ;;
esac
```

### Markdown Formatting Tips

- `*bold text*` - Bold
- `_italic text_` - Italic
- `` `code` `` - Inline code
- `[text](url)` - Hyperlink
- `%0A` - Line break in URL-encoded message

## Build Optimizations

### ccache Size
```yaml
ccache --max-size=5G  # Increase for faster builds
```

### Parallel Jobs
```yaml
make -j$(nproc --all)  # Use all CPU cores
# or
make -j8  # Limit to 8 cores
```

### Compiler Flags

Add custom flags in build step:

```yaml
CFLAGS="-O3 -march=armv8.2-a" \
make -j$(nproc) O=out ...
```

## Artifact Retention

Change how long artifacts are kept:

```yaml
retention-days: 30  # Keep for 30 days
# or
retention-days: 7   # Keep for 7 days (saves storage)
# or
retention-days: 90  # Keep for 90 days (max)
```

## Adding More Defconfigs

Support multiple device variants:

```yaml
workflow_dispatch:
  inputs:
    device:
      description: 'Device Variant'
      type: choice
      options:
        - 'RM6785'
        - 'RMX2001'
        - 'RMX2002'
```

Then use `${{ github.event.inputs.device }}_defconfig` in build step.

## Schedule Builds

Add automatic nightly builds:

```yaml
on:
  workflow_dispatch:
    # ... existing inputs
  schedule:
    - cron: '0 0 * * *'  # Daily at midnight UTC
```

Common cron schedules:
- `0 0 * * *` - Daily at midnight
- `0 0 * * 0` - Weekly on Sunday
- `0 0 1 * *` - Monthly on 1st
- `0 */6 * * *` - Every 6 hours

## Advanced: Multiple Jobs

Build multiple variants in parallel:

```yaml
strategy:
  matrix:
    ksu: [None, RKSU, SukiSU]
    susfs: [true, false]
```

## Performance Tips

1. **Use ccache** - Enabled by default, speeds up rebuilds
2. **Shallow clone** - `--depth=1` for faster git clone
3. **Parallel builds** - Use all CPU cores with `-j$(nproc)`
4. **Cache toolchain** - Reuse clang across builds
5. **Minimize logs** - Redirect verbose output with `> /dev/null 2>&1`

## Security Notes

- Never commit secrets to repository
- Use GitHub Secrets for sensitive data
- Rotate tokens periodically
- Use minimal bot permissions

## Debugging

Enable debug logs:

```yaml
- name: Debug Info
  run: |
    echo "Kernel dir: $KERNEL_DIR"
    echo "Defconfig: $DEFCONFIG"
    ls -la kernel/arch/arm64/configs/
    cat kernel/.config | grep KSU
```

## Common Issues

### Build fails with "Image.gz not found"
- Check defconfig exists
- Verify kernel compiles standalone
- Check for compilation errors in logs

### Telegram not working
- Verify bot token format
- Check chat ID is correct
- Ensure bot is admin (for channels)
- Test bot with `/start` command

### ccache not speeding up builds
- Check cache is being restored
- Verify ccache stats with `ccache -s`
- Increase cache size if needed

### Out of disk space
- Reduce ccache size
- Clean artifacts regularly
- Use shallow clones
