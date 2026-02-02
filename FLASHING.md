# How to Flash GTA4XLVE KernelSU Kernel

This guide will help you flash the custom KernelSU kernel on your Samsung Galaxy Tab S6 Lite (GTA4XLVE).

## ⚠️ Prerequisites

Before flashing, ensure you have:

1. **Unlocked Bootloader** - Your device must have an unlocked bootloader
2. **Recovery or Fastboot Access** - One of the following:
   - LineageOS Recovery (built-in with LineageOS)
   - TWRP or OrangeFox recovery
   - Fastboot/ADB tools on your computer
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
- Android 15
- Android 16 (including QPR2)
- LineageOS 18.x - 23.x (latest: 23.2)

## 🔧 Flashing Instructions

### Method 1: Using LineageOS Recovery (For LineageOS Users) ⭐ RECOMMENDED FOR LINEAGEOS

This method works with the built-in LineageOS Recovery - no need for TWRP!

1. **Download the Kernel ZIP**
   - Download the latest `gta4xlve-ksun-YYYYMMDD.zip` from GitHub Actions artifacts
   - Transfer it to your computer

2. **Boot into LineageOS Recovery**
   - Turn off your device
   - Press and hold `Volume Up + Power` buttons simultaneously
   - When you see the LineageOS Recovery screen, release the buttons
   - You'll see the LineageOS Recovery menu (text-based, no touch)

3. **Navigate to Apply Update**
   - Use **Volume buttons** to navigate (Up/Down)
   - Use **Power button** to select
   - Navigate to **"Apply update"** and press Power
   - Select **"Apply from ADB"**

4. **Flash via ADB Sideload**
   On your computer, run:
   ```bash
   adb sideload gta4xlve-ksun-YYYYMMDD.zip
   ```
   
   Wait for the process to complete. You'll see progress on both device and computer.

5. **Reboot**
   - Navigate back to main menu
   - Select **"Reboot system now"**
   - Your device will boot with the new kernel

**Navigation Tips for LineageOS Recovery**:
- Volume Up = Move up in menu
- Volume Down = Move down in menu
- Power Button = Select/Confirm
- No touch screen - use buttons only!

### Method 2: Using TWRP/OrangeFox Recovery

This method is for users who have custom recovery installed.

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

### Method 3: Using Fastboot (Advanced)

For advanced users who want to flash without recovery or have boot issues.

**Requirements**:
- Fastboot tools installed on your computer
- USB debugging enabled (if device boots)
- Unlocked bootloader

**Steps**:
1. Extract `Image.gz` from the kernel ZIP file
2. Boot device into fastboot mode:
   ```bash
   adb reboot bootloader
   ```
   Or use button combination: `Volume Down + Power`

3. Flash the kernel:
   ```bash
   fastboot flash boot Image.gz
   ```

4. Reboot:
   ```bash
   fastboot reboot
   ```

**Note**: This method flashes only the kernel image without using the AnyKernel3 installer. Use only if you know what you're doing.

## ✅ Verifying Installation

After booting, verify KernelSU is installed:

1. **Check Kernel Version**
   - Go to **Settings** → **About tablet** → **Software information**
   - Look for "Kernel version" - should show custom kernel info

2. **Install KernelSU-Next Manager**
   
   **IMPORTANT**: You need **KernelSU-Next Manager**, not original KernelSU Manager!
   
   - **Download from**: https://github.com/rifsxd/KernelSU-Next/releases
   - **Get the latest APK** (any v1.x.x version works)
   - Install the APK on your device
   
   **Why KernelSU-Next Manager?**
   - This kernel uses KernelSU-Next (legacy branch)
   - Original KernelSU Manager from tiann is **NOT compatible**
   - See **MANAGER_VERSION.md** for full details

3. **Verify in Manager App**
   - Open KernelSU-Next Manager
   - Should show kernel version: **32956** (or higher)
   - Root status should be **Granted**
   - If it shows version 0 or "unsupported", reflash the kernel

## 🔄 Reverting to Stock Kernel

If you need to go back to stock kernel:

### For LineageOS Users:
1. **Dirty Flash LineageOS**:
   - Download your current LineageOS version
   - Flash it in recovery (this will restore stock kernel)
   - No data loss - it's a "dirty flash"

2. **Or reflash boot.img**:
   - Extract boot.img from LineageOS ZIP
   - Flash via fastboot: `fastboot flash boot boot.img`

### For Other ROMs:
1. Flash your device's stock firmware via Odin (Windows) or Heimdall (Linux/Mac)
2. Or restore a backup of your boot partition from TWRP

## ⚙️ What This Kernel Includes

- **KernelSU-Next**: Root solution integrated into the kernel
- **Manual Hooks**: Pre-integrated KernelSU hooks for better compatibility
- **Optimizations**: Built with LLVM/Clang for better performance
- **Overlay Support**: Device tree overlays for proper hardware support
- **LineageOS Compatible**: Tested and working on LineageOS 18-23 (including 23.2)

## 🐛 Troubleshooting

### Device Stuck at Boot Logo
- **Solution**: Boot into recovery and flash stock boot.img or restore backup
- For LineageOS: Dirty flash your LineageOS ROM
- This usually means kernel incompatibility with your ROM

### KernelSU Not Working
- **Check**: Ensure you installed the KernelSU Manager app
- **Check**: Kernel version in Settings shows the custom kernel
- **Try**: Reinstall the kernel ZIP
- **LineageOS**: Make sure you're on a compatible LineageOS version (18-23, including 23.2)
- **Android 16**: Fully supported including QPR2

### Boot Loop
- **Solution**: Boot into recovery, wipe cache and dalvik cache
- **If persists**: Flash stock firmware or LineageOS ROM again

### Module Installation Fails
- **Check**: Ensure you're using modules compatible with KernelSU
- **Note**: Magisk modules are NOT compatible with KernelSU

### ADB Sideload Not Working
- **LineageOS Recovery**: Make sure USB debugging was enabled before booting to recovery
- **Computer**: Install latest ADB platform tools
- **Cable**: Try a different USB cable (some cables are charge-only)
- **Port**: Try different USB port on your computer
- **Driver**: Install Samsung USB drivers (Windows) or set up udev rules (Linux)

### LineageOS Recovery Doesn't Show "Apply from ADB"
- Make sure you're in the correct menu
- Select "Apply update" first, then "Apply from ADB"
- If missing, your recovery might be too old - update LineageOS

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
