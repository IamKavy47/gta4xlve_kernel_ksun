# KernelSU Manager - Which Version to Install?

## Quick Answer

For **KernelSU-Next** (legacy branch, kernel 4.14), you need:

**KernelSU-Next Manager**
- **Download from**: https://github.com/rifsxd/KernelSU-Next/releases
- **Latest version**: Download the most recent APK (currently v1.x.x)
- **NOT** the original KernelSU Manager from tiann

## Important: KernelSU-Next vs Original KernelSU

### KernelSU-Next (What You're Using)

- **Repository**: https://github.com/rifsxd/KernelSU-Next or https://github.com/KernelSU-Next/KernelSU-Next
- **Manager App**: https://github.com/rifsxd/KernelSU-Next/releases
- **For**: Legacy kernels including 4.14
- **Features**: Enhanced SUSFS support, legacy compatibility

### Original KernelSU (Different Project)

- **Repository**: https://github.com/tiann/KernelSU
- **Manager App**: https://github.com/tiann/KernelSU/releases
- **For**: Modern kernels 5.10+
- **NOT compatible** with KernelSU-Next

## Installation Steps

### 1. Download the Correct Manager

Go to: **https://github.com/rifsxd/KernelSU-Next/releases**

Download the latest APK file named something like:
- `KernelSU-Next_Manager_v1.x.x.apk`
- Or `ksunext-manager-vX.X.X-release.apk`

### 2. Install the APK

```bash
# Via ADB
adb install KernelSU-Next_Manager_vX.X.X.apk

# Or manually:
# - Transfer APK to device
# - Enable "Install from unknown sources"
# - Tap the APK to install
```

### 3. Open and Verify

After installation:
1. Open **KernelSU-Next Manager** app
2. It should detect your kernel version: **32956** (or similar)
3. Root status should show as **Granted**

## Version Compatibility

| Kernel | Branch | Manager Version |
|--------|--------|-----------------|
| 4.4 - 5.4 | legacy | KernelSU-Next Manager (any recent version) |
| 5.10+ | dev | KernelSU-Next Manager (any recent version) |

**All KernelSU-Next manager versions** work with the legacy branch kernel!

## Troubleshooting

### "Version too old" Error

If you see:
```
KernelSU version: 0 (kernel)
! KernelSU version is too old!
```

**Solution**: You flashed the wrong kernel. Reflash using the kernel built with legacy branch (version 32956+).

### Manager Shows "Unsupported"

**Possible causes**:
1. ❌ You installed **original KernelSU Manager** (wrong app)
   - **Fix**: Uninstall and install **KernelSU-Next Manager**

2. ❌ Kernel version is 0 (build issue)
   - **Fix**: Reflash kernel from latest build

3. ❌ SELinux is Enforcing (some ROMs)
   - **Fix**: Kernel should handle this, but check SELinux status

### Can't Install Modules

Make sure you're using:
- ✅ **KernelSU-Next Manager** app
- ✅ Kernel with version **32956** or higher (not 0)
- ✅ **KernelSU modules** (not Magisk modules - they're different!)

## Module Compatibility

### ✅ Compatible Modules
- Zygisk-Next (LSposed)
- KernelSU-specific modules
- Modules marked as "KernelSU compatible"

### ❌ NOT Compatible
- Magisk modules (different framework)
- Modules requiring Magisk-specific APIs

## Alternative Manager Apps

Some users report these also work with KernelSU-Next:

1. **KSU Manager** (community fork)
2. **APatch Manager** (in some cases)

But the **official KernelSU-Next Manager** is recommended.

## Where to Find Manager

### Official Source (Recommended)
```
https://github.com/rifsxd/KernelSU-Next/releases
https://github.com/KernelSU-Next/KernelSU-Next/releases
```

### Telegram Channel
Many users get updates from the KernelSU-Next Telegram channel where APKs are shared.

## Verification

After installing the manager and rebooting:

1. **Open KernelSU-Next Manager**
2. **Check version info**:
   ```
   Kernel: 32956
   Manager: 1.x.x
   Status: Working
   ```

3. **Test root**:
   - Grant root to any app (like Termux)
   - Run `su` command
   - Should get root shell with `#` prompt

4. **Try installing a module**:
   - Install Zygisk-Next
   - Should install without "version too old" error

## Summary

**For kernel 4.14 with legacy branch**:

| What | Where |
|------|-------|
| **Manager to Use** | KernelSU-Next Manager |
| **Download Link** | https://github.com/rifsxd/KernelSU-Next/releases |
| **Version** | Latest available (any v1.x.x works) |
| **Kernel Version** | 32956+ (from legacy branch) |
| **Wrong Manager** | Original KernelSU from tiann (don't use!) |

---

**Last Updated**: 2026-02-02  
**For**: Kernel 4.14 with KernelSU-Next legacy branch
