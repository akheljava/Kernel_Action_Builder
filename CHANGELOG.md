# Changelog

## v2.0 - Major Improvements (December 2024)

### 🎯 New Features

#### Branch Selection
- **Custom branch input**: Choose any kernel branch during workflow run
- No longer hardcoded to `main` branch
- Supports branches like `main`, `master`, `lineage-21.0`, etc.

#### Improved KSU Logic
- **Enable KSU checkbox**: Clear on/off toggle for KSU support
- **Variant selection**: Only relevant when KSU is enabled
- **Smart SUSFS handling**: SUSFS requires KSU to be enabled first
- **xxKSU warning**: Automatically warns if SUSFS selected with xxKSU

#### Enhanced Naming
- **Clear zip names**: Shows KSU variant and SUSFS status
  - Stock: `Miku-RM6785-Stock-TIMESTAMP.zip`
  - KSU: `Miku-RM6785-RKSU-TIMESTAMP.zip`
  - KSU+SUSFS: `Miku-RM6785-RKSU-SUSFS-TIMESTAMP.zip`

### 📱 Telegram Improvements

#### Better Notifications
- **Hyperlinked manager downloads**: Clean links instead of full URLs
- **Conditional note display**: Notes only shown when provided
- **Rich formatting**: Better emoji usage and organization
- **Branch information**: Shows which kernel branch was built
- **Manager links per variant**:
  - RKSU: Links to rsuntk/KernelSU releases
  - xxKSU: Links to backslashxx/KernelSU releases
  - SukiSU: Links to SukiSU-Ultra releases

#### Success Notification Shows:
- Device and kernel version
- Build variant with SUSFS status
- Image size in MB
- Branch and commit info
- Build duration
- KSU commit with link
- Manager download (hyperlinked)
- Custom note (only if provided)

#### Failure Notification Shows:
- Device and attempted variant
- Branch and commit
- Custom note (only if provided)
- Direct link to error logs

### 🔧 Technical Improvements

#### Workflow Logic
- **Conditional steps**: Steps only run when needed
- **Environment variables**: Better state management
- **SUSFS validation**: Only enables with compatible variants
- **Stock build support**: Clean builds without KSU

#### Code Quality
- Cleaner conditional logic
- Better variable naming
- Improved error handling
- More descriptive step names

### 🐛 Bug Fixes
- Fixed SUSFS being selectable without KSU
- Fixed naming inconsistencies
- Fixed note showing even when empty
- Fixed manager URL visibility

### 📝 Documentation Updates
- Updated README with new workflow options
- Updated QUICKSTART guide
- Added CHANGELOG
- Better examples throughout

---

## v1.0 - Initial Release

### Features
- Basic kernel compilation
- KSU variant support (RKSU, xxKSU, SukiSU)
- SUSFS support
- AnyKernel3 packaging
- Telegram notifications
- ccache support
- GitHub Actions caching

---

## Upgrade Guide

If you're upgrading from v1.0:

1. **Replace workflow file**: Update `.github/workflows/build-kernel.yml`
2. **No secret changes needed**: Existing secrets work as-is
3. **New workflow inputs**: Note the new checkbox-based KSU enable option
4. **Test first**: Run a stock build to verify everything works

### Breaking Changes
- Workflow input structure changed
- `ksu_variant` dropdown replaced with checkbox + dropdown combo
- Branch selection now required (defaults to 'main')

### Migration Tips
- Old workflow runs will still work with artifacts
- Update any automation scripts to use new input names
- Branch parameter is now mandatory (previously implicit)
