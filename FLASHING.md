# How to Flash GTA4XLVE KernelSU Kernel

This guide will help you flash the custom KernelSU kernel on your Samsung Galaxy Tab S6 Lite (GTA4XLVE).

## ⚠️ Prerequisites

Before flashing, ensure you have:

1. **Unlocked Bootloader** - Your device must have an unlocked bootloader
2. **Custom Recovery** - TWRP or OrangeFox recovery installed
3. **Backup** - Complete backup of your data (flashing can cause data loss)
4. **Charged Battery** - At least 50% battery charge
5. **Downloaded Kernel ZIP** - The `gta4xlve-ksun-YYYYMMDD.zip` file from GitHub Actions

## 📱 Supported Devices

- Samsung Galaxy Tab S6 Lite WiFi (SM-P610)
- Samsung Galaxy Tab S6 Lite LTE (SM-P613)
- Model: gta4xlve / gta4xl

## 📋 Supported Android Versions

- Android 11
- Android 12
- Android 13
- Android 14

## 🔧 Flashing Instructions

### Method 1: Using TWRP/OrangeFox Recovery (Recommended)

1. **Download the Kernel ZIP**
   - Download the latest `gta4xlve-ksun-YYYYMMDD.zip` from GitHub Actions artifacts
   - Transfer it to your device's internal storage or SD card

2. **Boot into Recovery**
   - Turn off your device
   - Press and hold `Volume Up + Power` buttons simultaneously
   - When you see the Samsung logo, release the buttons
   - You'll enter recovery mode

3. **Flash the Kernel**
   - In TWRP/OrangeFox, tap **Install**
   - Navigate to where you saved the kernel ZIP
   - Select `gta4xlve-ksun-YYYYMMDD.zip`
   - Swipe to confirm flash
   - Wait for the process to complete (usually 10-30 seconds)

4. **Reboot**
   - Tap **Reboot System**
   - Your device will boot with the new kernel

### Method 2: Using ADB Sideload

1. **Enable ADB Sideload in Recovery**
   - Boot into TWRP/OrangeFox recovery
   - Tap **Advanced** → **ADB Sideload**
   - Swipe to start sideload

2. **Flash via ADB**
   ```bash
   adb sideload gta4xlve-ksun-YYYYMMDD.zip
   ```

3. **Reboot**
   - After flashing completes, tap **Reboot System**

## ✅ Verifying Installation

After booting, verify KernelSU is installed:

1. **Check Kernel Version**
   - Go to **Settings** → **About tablet** → **Software information**
   - Look for "Kernel version" - should show custom kernel info

2. **KernelSU Manager**
   - Install KernelSU Manager app from GitHub: https://github.com/tiann/KernelSU/releases
   - Open the app to verify KernelSU is working
   - You should see the kernel version and root access status

## 🔄 Reverting to Stock Kernel

If you need to go back to stock kernel:

1. Flash your device's stock firmware via Odin (Windows) or Heimdall (Linux/Mac)
2. Or restore a backup of your boot partition from TWRP

## ⚙️ What This Kernel Includes

- **KernelSU-Next**: Root solution integrated into the kernel
- **Manual Hooks**: Pre-integrated KernelSU hooks for better compatibility
- **Optimizations**: Built with LLVM/Clang for better performance
- **Overlay Support**: Device tree overlays for proper hardware support

## 🐛 Troubleshooting

### Device Stuck at Boot Logo
- **Solution**: Boot into recovery and flash stock boot.img or restore backup
- This usually means kernel incompatibility with your ROM

### KernelSU Not Working
- **Check**: Ensure you installed the KernelSU Manager app
- **Check**: Kernel version in Settings shows the custom kernel
- **Try**: Reinstall the kernel ZIP

### Boot Loop
- **Solution**: Boot into recovery, wipe cache and dalvik cache
- **If persists**: Flash stock firmware

### Module Installation Fails
- **Check**: Ensure you're using modules compatible with KernelSU
- **Note**: Magisk modules are NOT compatible with KernelSU

## 📚 Additional Resources

- **KernelSU Documentation**: https://kernelsu.org/
- **KernelSU GitHub**: https://github.com/tiann/KernelSU
- **Device Tree Source**: https://github.com/gta4xlve-dev
- **Report Issues**: https://github.com/IamKavy47/gta4xlve_kernel_ksun/issues

## ⚠️ Disclaimer

- Flashing custom kernels voids your warranty
- The developers are not responsible for bricked devices, dead SD cards, thermonuclear war, or you getting fired because the alarm app failed
- YOU are choosing to make these modifications, and if you point the finger at us for messing up your device, we will laugh at you
- Your warranty is now void. Have fun!

## 💡 Tips

1. **Always backup** before flashing anything
2. **Read the changelogs** to know what's new/fixed
3. **Check compatibility** with your ROM before flashing
4. **Join community forums** for support and updates
5. **Keep stock boot.img** as backup for easy restoration

---

**Last Updated**: 2026-02-02
**Kernel Version**: Built from latest source with KernelSU-Next integration
