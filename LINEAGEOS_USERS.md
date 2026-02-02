# LineageOS Users - Flash Without TWRP! 🎉

## Good News for LineageOS Users!

You **DO NOT need TWRP** to flash this kernel! Use your built-in LineageOS Recovery.

## 🚀 Quick Steps

1. **Download** `gta4xlve-ksun-YYYYMMDD.zip` from GitHub Actions
2. **Boot to LineageOS Recovery**:
   - Power off device
   - Hold `Volume Up + Power` until you see LineageOS Recovery
3. **Flash via ADB Sideload**:
   - Navigate to: **Apply update** → **Apply from ADB**
   - On your PC: `adb sideload gta4xlve-ksun-YYYYMMDD.zip`
4. **Reboot** and install KernelSU Manager app

## 📱 Navigation in LineageOS Recovery

LineageOS Recovery is **text-based** (no touch):
- **Volume Up** = Move up
- **Volume Down** = Move down
- **Power Button** = Select/Confirm

## ✅ What You Need

- ✓ LineageOS installed (any version 18-21)
- ✓ Unlocked bootloader
- ✓ ADB tools on your computer
- ✓ USB cable
- ✓ This kernel ZIP file

## 💡 Why This Method?

- **No extra recovery needed** - Use what you already have
- **Official and trusted** - LineageOS's own recovery
- **Simple and safe** - No need to install/maintain TWRP
- **Data safe** - Non-destructive installation

## 🔄 If Something Goes Wrong

**Bootloop or won't boot?**
- Boot back to LineageOS Recovery
- Wipe cache partition
- If still fails: Dirty flash your LineageOS ROM (keeps your data!)

**Dirty Flash Steps**:
1. Boot to LineageOS Recovery
2. Apply update → Apply from ADB
3. `adb sideload lineage-xxx.zip` (your current LineageOS version)
4. This restores stock kernel without losing data

## ⚠️ Important Notes

1. **KernelSU ≠ Magisk**
   - Different root solutions
   - KernelSU modules only (not Magisk modules)
   - Install KernelSU Manager, not Magisk Manager

2. **USB Debugging**
   - Should be enabled before booting to recovery
   - Helps with ADB recognition

3. **Driver Issues?**
   - Windows: Install Samsung USB drivers
   - Linux: Set up udev rules
   - Try different USB ports/cables

## 📚 More Details

See **FLASHING.md** for comprehensive guide with:
- Detailed step-by-step instructions
- Troubleshooting section
- Alternative methods (Fastboot, TWRP)
- FAQ and tips

## 🎯 Quick Verification

After flashing and booting:
1. Settings → About → Software info → Check kernel version
2. Install KernelSU Manager app
3. Open app to verify root access

## 💬 Support

- Issues: https://github.com/IamKavy47/gta4xlve_kernel_ksun/issues
- Make sure you mention you're using LineageOS!

---

**TL;DR**: LineageOS users don't need TWRP - use your built-in recovery with ADB sideload! 🎊
