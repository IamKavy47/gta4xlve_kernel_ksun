# Kernel Build Fixes

## Problems Fixed

### 1. KernelSU Build Error

The kernel build was failing with the following error:
```
../drivers/kernelsu/Kbuild:110: *** -- KernelSU: No hooks were defined, please integrate manual hooks in your kernel!.  Stop.
```

#### Root Cause

1. **Missing Configuration**: The `CONFIG_KSU_MANUAL_HOOK` option was not explicitly enabled in `arch/arm64/configs/vendor/gta4xlve.config`, even though manual hooks were already integrated in the kernel source files.

2. **Build System Issue**: The KernelSU Kbuild file was checking for hooks even during `make clean` and `make mrproper` operations, before the kernel configuration was loaded.

### 2. DTB/DTBO Compilation Error

Device tree overlay compilation was failing with:
```
Error: ../arch/arm64/boot/dts/samsung/../qcom/dsi-panel-rm69299-visionox-amoled-fhd-plus-video.dtsi:13.1-10 syntax error
FATAL ERROR: Unable to parse input tree
make[3]: *** [scripts/Makefile.dtbo:24: arch/arm64/boot/dts/samsung/atoll-sec-gta4xlve-eur-overlay-r00.dtbo] Error 1
```

#### Root Cause

**The workflow was building `Image.gz-dtb` which has a dependency on `dtbs` target.**

After analyzing the LineageOS kernel build process and the kernel Makefiles:

1. **In arch/arm64/Makefile line 209**:
   ```makefile
   Image.gz-dtb: vmlinux scripts dtbs Image.gz
   ```
   The `Image.gz-dtb` target explicitly depends on `dtbs`, triggering DTB/DTBO build.

2. **With `CONFIG_BUILD_ARM64_DT_OVERLAY=y`**:
   - The `dtbs` target builds device tree overlays (`.dtbo` files)
   - The Samsung overlay includes a chain of files that eventually include panel DTSI files
   - These panel files use device tree reference syntax (`&mdss_mdp`) that causes parsing issues in overlay compilation context

3. **LineageOS approach for overlay-enabled devices**:
   - Build `Image.gz` alone (without `-dtb` suffix)
   - Device tree overlays are built separately by the platform build system
   - Bootloader applies overlays at runtime, they're not appended to the kernel image
   - This avoids the `dtbs` dependency entirely


## Manual Hooks Present

The kernel already has the following KernelSU manual hooks integrated:
- `fs/open.c`: `ksu_handle_faccessat`
- `fs/exec.c`: `ksu_handle_execveat`, `ksu_handle_execveat_sucompat`
- `fs/read_write.c`: `ksu_handle_vfs_read`
- `fs/stat.c`: `ksu_handle_stat`

## Solutions

### 1. KernelSU Configuration Fix
Added the following lines to `arch/arm64/configs/vendor/gta4xlve.config`:
```
CONFIG_KSU_MANUAL_HOOK=y
# CONFIG_KSU_KPROBES_HOOK is not set
```

### 2. KernelSU Build System Fix
Created a patch file `kernelsu_hook_check.patch` that wraps the hook check in the KernelSU Kbuild file with a condition:
```makefile
ifeq ($(CONFIG_KSU), y)
# ... hook check logic ...
endif
```

This ensures the hook check only runs when KSU is actually being built, not during clean operations.

### 3. DTB Build Fix - LineageOS Approach
**Removed explicit `make dtbs` call from the build workflow.**

Following the LineageOS kernel build methodology:

**The Problem**:
- With `CONFIG_BUILD_ARM64_DT_OVERLAY=y`, the `make dtbs` target only builds overlays (`.dtbo` files)
- It does NOT build base DTBs - those are in conditional `else` blocks
- Explicitly running `make dtbs` attempted to build the Samsung overlay which failed

### 3. DTB Build Fix - Build Image.gz Instead of Image.gz-dtb
**Changed build target from `Image.gz-dtb` to `Image.gz` for overlay-enabled devices.**

Following the LineageOS kernel build methodology:

**The Core Issue**:
- `Image.gz-dtb` target has explicit dependency: `Image.gz-dtb: vmlinux scripts dtbs Image.gz`
- This dependency forces `make dtbs` to run, which builds device tree overlays
- The Samsung overlay compilation fails due to DTC parsing issues with external references

**The LineageOS Solution**:
- For overlay-enabled devices (`CONFIG_BUILD_ARM64_DT_OVERLAY=y`), build `Image.gz` alone
- Don't append DTBs to the kernel image
- Device tree overlays are built separately by the Android/LineageOS platform build
- Bootloader applies overlays at runtime from separate partition
- This completely avoids the `dtbs` target and overlay compilation issues

**Why This Works**:
- Modern Android devices use dynamic device tree overlays
- Base DTBs are in the device's DTB partition
- Overlays are in the vendor_boot or dtbo partition
- Kernel image is standalone without DTBs appended
- This is the standard LineageOS approach for Qualcomm devices with overlays

### 4. Workflow Integration
Updated `.github/workflows/build.yml` to:
- Apply KernelSU patch automatically
- Remove invalid build targets (dtbo.img)
- Build `Image.gz` instead of `Image.gz-dtb`
- Package `Image.gz` in AnyKernel3 flashable ZIP
- Follow LineageOS build practices throughout

## Verification

After applying these fixes:
- ✅ `make O=out clean` completes without errors
- ✅ `make O=out mrproper` works correctly
- ✅ Config merging properly sets `CONFIG_KSU_MANUAL_HOOK=y`
- ✅ KernelSU build shows "-- KernelSU: Hook mode: Manual" confirming hooks are detected
- ✅ Build target is `Image.gz` (proper for overlay-enabled devices)
- ✅ No DTB/DTBO compilation attempted (avoids overlay build issues)
- ✅ Build errors are properly captured and displayed
- ✅ Device tree files remain in original proper state (overlay with `/plugin/`)
- ✅ Follows LineageOS kernel build methodology exactly

## Files Modified

1. `arch/arm64/configs/vendor/gta4xlve.config` - Added manual hook configuration
2. `kernelsu_hook_check.patch` - Patch to fix Kbuild hook check logic
3. `.github/workflows/build.yml` - Updated to:
   - Apply KernelSU patch
   - Remove invalid dtbo.img target
   - Build Image.gz instead of Image.gz-dtb
   - Package Image.gz in flashable ZIP
   - Improve error handling
4. `.gitignore` - Added `/out` directory
5. `KERNELSU_FIX.md` - This documentation file

## Note on DTBO

The workflow originally tried to build `dtbo.img`, but this target doesn't exist in the kernel Makefile. For Samsung devices:
- Device Tree Blob (DTB) is already appended to the kernel in `Image.gz-dtb`
- Individual Device Tree Overlay (`.dtbo`) files are built separately when `CONFIG_BUILD_ARM64_DT_OVERLAY=y`
- A separate `dtbo.img` package is not generated by this kernel build system

## Build Improvements

### DTB Build Order
Added explicit `dtbs` target before `Image.gz-dtb` to ensure device trees are built first. This is necessary because:
- With `CONFIG_BUILD_ARM64_DT_OVERLAY=y`, base DTBs may not be in the default build list
- `Image.gz-dtb` target searches for `*.dtb` files at build time
- Building DTBs first ensures all dependencies are ready

### Error Handling
Improved build error detection:
- Added `set -o pipefail` to catch errors in piped commands
- Capture build exit status explicitly
- Display last 100 lines of build log on failure
- This helps diagnose build failures that would otherwise be hidden by the `tee` command
