# GTA4XLVE Kernel with KernelSU-Next - Complete Guide

## Table of Contents
1. [Flashing Instructions](#flashing-instructions)
2. [KernelSU Manager](#kernelsu-manager)
3. [Module Installation](#module-installation)
4. [Technical Details](#technical-details)

---

## Flashing Instructions

### Prerequisites

- ✅ Samsung Galaxy Tab S6 Lite (gta4xlve)
- ✅ LineageOS 23
- ✅ Unlocked bootloader
- ✅ LineageOS Recovery or TWRP installed
- ✅ ADB/Fastboot installed on PC
- ✅ USB debugging enabled (if using ADB sideload)

### Method 1: LineageOS Recovery (Recommended)

**Best for**: LineageOS users who don't want custom recovery

**Steps**:
1. **Boot to Recovery**:
   - Power off device
   - Hold Power + Volume Up

2. **Enable ADB Sideload**:
   - Use Volume Up/Down to navigate
   - Select "Apply update"
   - Select "Apply from ADB"
   - Power button to confirm

3. **Flash Kernel**:
   ```bash
   adb sideload gta4xlve-ksun-YYYYMMDD.zip
   ```
   Tap yes if it sasys verification failed u wanna continue

4. **Reboot**:
   - Select "Reboot system now"
   - Device will boot with new kernel

## KernelSU Manager

### Which Manager to Use

**✅ CORRECT Manager**:
- **Name**: KernelSU-Next Manager
- **Source**: https://github.com/KernelSU-Next/KernelSU-Next/releases/download/v1.1.1/KernelSU_Next_v1.1.1_12851-release.apk

### Installation

**Option 1: Manual Install**:
1. Download APK from releases page
2. Install: `KernelSU_Manager_*.apk`
3. Open app and grant permissions

### Verification

After installation, open Manager:

**Should show**:
```
✅ KernelSU-Next
✅ Version: [Version from v1.1.1 tag] (kernel)
✅ Manager: v1.1.1 or v3.0.x or v3.1.x
✅ Status: Working
✅ Root access: Available
```

**If you see**:
```
❌ Version: 0 (kernel)
❌ Status: Unsupported
❌ Signature not found
```
→ Wrong manager or kernel not properly installed. Reflash kernel.

### Manager Versions

| Version | Status | Features | Notes |
|---------|--------|----------|-------|
| 1.0.x | ✅ | Basic root, modules | Early release |
| 1.1.0 | ✅ | v2 signature | Improved |
| 1.1.1 | ✅ | Latest 1.x | Stable |
| 3.0.x | ✅ | v3 features | Modern UI |
| 3.1.x | ✅ | Latest | Most features |

**All versions work with KernelSU-Next v1.1.1 tag!**

---

## Module Installation

### Supported Modules

**✅ Working Modules**:
- Zygisk-Next (LSPosed framework)
- KernelSU-specific modules
- System tweaks (AdAway, etc.)
- Xposed-style modules via Zygisk

**❌ Not Supported**:
- Magisk modules (different framework)
- Modules requiring Magisk-specific features

### Installing Modules

**Via Manager**:
1. Open KernelSU-Next Manager
2. Tap "Modules" tab
3. Tap "+" button
4. Select module ZIP
5. Install and reboot

**Via ADB**:
```bash
adb push module.zip /sdcard/
# Then install via Manager
```

### Zygisk-Next Example

**Before Fix**:
```
- Module size: 9.69 MB
- Installing to /data/adb/modules_update/zygisksu
- KernelSU version: 0 (kernel) + 32857 (ksud)
! KernelSU version is too old!
! Please update KernelSU to latest version
Error: Failed to install module script
```

**After Fix**:
```
- Module size: 9.69 MB
- Installing to /data/adb/modules_update/zygisksu
- KernelSU version: [proper version] (kernel) + [ksud version] (ksud)
✓ Installation successful
✓ Reboot to enable module
```

### Module Troubleshooting

**Module installs but doesn't work**:
1. Check module compatibility with KernelSU-Next
2. Reboot after installation
3. Check module log in Manager
4. Some modules need configuration

**Module fails to install**:
1. Verify kernel version is detected (not 0)
2. Update Manager to latest
3. Clear Manager cache
4. Try different module version

---

## Technical Details

### Kernel Information

**Base**:
- Kernel: Linux 4.14
- Device: Samsung Galaxy Tab S6 Lite (gta4xlve)
- Platform: Qualcomm Atoll (SM6150)
- Architecture: ARM64

**KernelSU Integration**:
- Type: KernelSU-Next (not original KernelSU)
- Version: v1.1.1 tag (stable release for kernel 4.14)
- URL: https://github.com/KernelSU-Next/KernelSU-Next/tree/v1.1.1
- Setup: Official setup script from KernelSU-Next
- Command: `curl -LSs "https://raw.githubusercontent.com/KernelSU-Next/KernelSU-Next/next/kernel/setup.sh" | bash -s v1.1.1`
- Manual Hooks: 5 hooks integrated
- Hook Check: ksu_handle_faccessat verified

### Manual Hooks Integrated

KernelSU-Next v1.1.1 requires manual hooks for kernel 4.14 (kprobes support limited):

1. **fs/exec.c**: `ksu_handle_execveat`, `ksu_handle_execveat_sucompat`
   - Purpose: Intercept program execution
   - **Required for**: Root permission management, su compatibility

2. **fs/open.c**: `ksu_handle_faccessat`
   - Purpose: Intercept file access checks
   - **Required for**: Access control, build verification (checked by Kbuild)

3. **fs/read_write.c**: `ksu_handle_vfs_read`
   - Purpose: Intercept VFS read operations
   - **Required for**: File read monitoring

4. **fs/stat.c**: `ksu_handle_stat`
   - Purpose: Intercept file stat operations
   - **Required for**: Metadata access control

5. **fs/devpts/inode.c**: `ksu_handle_devpts`
   - Purpose: Intercept devpts operations
   - **Required for**: PTY/terminal management

### Build Process

**Configuration**:
```makefile
CONFIG_KSU=y
CONFIG_KSU_MANUAL_HOOK=y
# CONFIG_KSU_KPROBES_HOOK is not set
```

**Version Calculation**:
```bash
# Version is calculated from v1.1.1 tag
Commit Count: (from git rev-list --count HEAD at v1.1.1)
KSU Version: 30000 + commit_count + 60
```

**Hook Check** (from KernelSU-Next/kernel/Kbuild):
```makefile
ifeq ($(CONFIG_KSU_MANUAL_HOOK), y)
HAVE_KSU_HOOK := $(shell grep -q "ksu_handle_faccessat" $(srctree)/fs/open.c && echo 0 || echo 1)
```

**Note**: v1.1.1 checks for `ksu_handle_faccessat` in fs/open.c, NOT sys_reboot

### Build Targets

- **Kernel Image**: `Image.gz` (not Image.gz-dtb)
- **Reason**: Device uses CONFIG_BUILD_ARM64_DT_OVERLAY=y
- **DTB/DTBO**: Handled by bootloader at runtime (LineageOS standard)

### AnyKernel3 Configuration

**Properties**:
```bash
device.name=gta4xlve
kernel=Image.gz
do.devicecheck=1
do.modules=0
do.cleanup=1
do.cleanuponabort=0
```

**Boot Attributes**:
```bash
block=/dev/block/bootdevice/by-name/boot
is_slot_device=0
ramdisk_compression=auto
```

### Version Support

**Android**: 11, 12, 13, 14, 15, 16 (QPR2)
**LineageOS**: 18.x, 19.x, 20.x, 21.x, 22.x, 23.x (23.2)

---

## Troubleshooting

### Build Issues

**"No hooks were defined" error**:
- **Cause**: Hook check fails during build
- **Solution**: Verify all 5 manual hooks are in kernel source
- **Check**: `grep "ksu_handle_faccessat" fs/open.c` should return results

**DTB compilation errors**:
- **Cause**: Trying to build incompatible DTB targets
- **Solution**: Build uses Image.gz only (no DTBs appended)
- **Note**: This is correct for overlay-enabled devices

### Manager Issues

**Manager shows "Unsupported"**:
- **Cause**: Wrong manager (original KernelSU instead of KernelSU-Next)
- **Solution**: Install correct manager from rifsxd's repo

**Version shows 0**:
- **Cause**: Kernel not properly flashed or wrong build
- **Solution**: Reflash kernel, verify version in build logs

**"Signature not found" (Manager 1.1.x)**:
- **Cause**: Incompatible KernelSU version or wrong build
- **Solution**: Flash latest build using v1.1.1 tag

### Module Issues

**"Version too old" when installing Zygisk-Next**:
- **Cause**: Kernel version reported as 0 or too low
- **Solution**: Reflash kernel with v1.1.1 tag, verify version detected

**Modules install but don't work**:
- **Cause**: Various - check specific module requirements
- **Solutions**:
  1. Reboot after module installation
  2. Check module logs in Manager
  3. Verify module compatibility with KernelSU-Next
  4. Some modules need additional configuration

### Boot Issues

**Device won't boot after flashing**:
- **Solution**: Boot to recovery, flash LineageOS boot.img to restore

**Bootloop**:
- **Solution**: Wipe cache/dalvik in recovery, or dirty flash ROM

**Safe Mode**: 
- Volume Down during boot = disable all KernelSU modules

### Recovery Methods

**Revert to Stock Kernel**:
```bash
# Method 1: Dirty flash LineageOS
- Boot to recovery
- Flash LineageOS ZIP (keeps data)

# Method 2: Flash boot.img from LineageOS
- Extract boot.img from LineageOS ZIP
- Boot to fastboot
- fastboot flash boot boot.img
```

**Keep Data Safe**:
- KernelSU only modifies kernel (boot partition)
- Your data is in /data partition (untouched)
- Reverting kernel doesn't erase data

---

## Additional Resources

### Links

- **This Repository**: https://github.com/IamKavy47/gta4xlve_kernel_ksun
- **KernelSU-Next**: https://github.com/KernelSU-Next/KernelSU-Next
- **Legacy Branch**: https://github.com/KernelSU-Next/KernelSU-Next/tree/legacy
- **Manager Releases**: https://github.com/rifsxd/KernelSU-Next/releases
- **Device Tree**: https://github.com/gta4xlve-dev/android_device_samsung_gta4xlveu
- **LineageOS**: https://github.com/LineageOS

### Support

**Issues**: Open an issue on this repository
**Logs**: Use `adb logcat` to capture kernel logs
**Manager Logs**: Available in Manager app

### Credits

- [@](https://github.com/DaViDev985)
- [@](https://github.com/danielml3)
- [@](https://github.com/iamkavy47)

---

## Summary

### What This Kernel Provides

✅ **Root Access**: Via KernelSU-Next framework  
✅ **Module Support**: Zygisk-Next and KernelSU modules  
✅ **Compatibility**: Android 16, LineageOS 23  
✅ **Stability**: Based on Samsung source with minimal changes  
✅ **Up-to-date**: KernelSU-Next v1.1.1 stable tag  


### Final Notes

This kernel is specifically built for **kernel 4.14**(lineageos23.2 - gta4xlveu) using **KernelSU-Next v1.1.1 tag**. This stable release is designed for older kernels that don't support kprobes properly.

**Version v1.1.1** is a proven stable release that works well with all current modules and manager versions. Everything should work out of the box!

Happy rooting! 🎉
