# GTA4XLVE Kernel with KernelSU-Next - Complete Guide

## Table of Contents
1. [Flashing Instructions](#flashing-instructions)
2. [KernelSU Manager](#kernelsu-manager)
3. [Module Installation](#module-installation)

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
   Tap yes if it says verification failed u wanna continue

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
- **Kernel Sourcehttps://github.com/gta4xlve-dev/android_kernel_samsung_gta4xlve** : 
- **LineageOS**: https://github.com/LineageOS

### Support

**Issues**: Open an issue on this repository
**Telegram**:[@](https://t.me/iamkavy47)iamkavy47

### Credits

- [@](https://github.com/DaViDev985)DaViDev985
- [@](https://github.com/danielml3)Danielml3
- [@](https://github.com/iamkavy47)Iamkavy47

---

## Summary

### What This Kernel Provides

✅ **Root Access**: Via KernelSU-Next framework  
✅ **Module Support**: Zygisk-Next and KernelSU modules  
✅ **Compatibility**: Android 16, LineageOS 23  
✅ **Up-to-date**: KernelSU-Next v1.1.1 stable tag  


### Final Notes

This kernel is specifically built for **kernel 4.14**(lineageos23.2 - gta4xlveu) using **KernelSU-Next v1.1.1 tag**. This stable release is designed for older kernels that don't support kprobes properly.

**Version v1.1.1** is a proven stable release that works well with all current modules and manager versions. Everything should work out of the box!

Happy rooting! 🎉
