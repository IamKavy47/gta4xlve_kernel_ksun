# Kernel Build Fixes

## Problems Fixed

### 1. KernelSU Build Error

The kernel build was failing with the following error:
```
../drivers/kernelsu/Kbuild:110: *** -- KernelSU: No hooks were defined, please integrate manual hooks in your kernel!.  Stop.
```

#### Root Cause

The issue had two parts:

1. **Missing Configuration**: The `CONFIG_KSU_MANUAL_HOOK` option was not explicitly enabled in `arch/arm64/configs/vendor/gta4xlve.config`, even though manual hooks were already integrated in the kernel source files.

2. **Build System Issue**: The KernelSU Kbuild file was checking for hooks even during `make clean` and `make mrproper` operations, before the kernel configuration was loaded.

### 2. DTB Compilation Error

Device tree compilation was failing with:
```
Error: ../arch/arm64/boot/dts/samsung/../qcom/dsi-panel-rm69299-visionox-amoled-fhd-plus-video.dtsi:13.1-10 syntax error
FATAL ERROR: Unable to parse input tree
```

#### Root Cause

**The workflow was incorrectly running `make dtbs` before `Image.gz-dtb`.**

After analyzing the LineageOS kernel build process, the issue was identified:

1. With `CONFIG_BUILD_ARM64_DT_OVERLAY=y`, the `dtbs` target only builds overlays (`.dtbo` files)
2. It does NOT build base DTBs (`.dtb` files) - those are in the `else` clause
3. The Samsung overlay includes qcom platform files that reference panel DTSI files
4. When explicitly running `make dtbs`, the build system tried to build ALL device trees including the problematic overlay chain
5. The overlay build failed due to how external references are handled in overlay context

**The LineageOS approach**: Don't explicitly build `dtbs`. Let `Image.gz-dtb` handle its own dependencies. The `Image.gz-dtb` target uses `$(shell find $(obj)/dts/ -name \*.dtb)` to find and append any existing base DTB files. If none exist (which is fine with overlays), it just appends an empty set.


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

**The Solution**:
- Don't explicitly call `make dtbs` before `Image.gz-dtb`
- Let `Image.gz-dtb` handle its own DTB dependencies
- The `Image.gz-dtb` Makefile target uses: `DTB_OBJS := $(shell find $(obj)/dts/ -name \*.dtb)`
- This finds and appends any existing base DTBs, or appends nothing if none exist (which is fine with overlays)

**Why This Works**:
- LineageOS builds base DTBs separately in the platform build
- Overlays are applied at runtime by the bootloader
- The kernel Image doesn't need DTBs appended when using overlays
- If base DTBs exist in the build output, they'll be found and appended
- If not (overlay-only mode), the kernel image is still valid

### 4. Workflow Integration
Updated `.github/workflows/build.yml` to automatically apply the KernelSU patch and follow LineageOS build practices.

## Verification

After applying these fixes:
- ✅ `make O=out clean` completes without errors
- ✅ `make O=out mrproper` works correctly
- ✅ Config merging properly sets `CONFIG_KSU_MANUAL_HOOK=y`
- ✅ KernelSU build shows "-- KernelSU: Hook mode: Manual" confirming hooks are detected
- ✅ Build targets only `Image.gz-dtb` (dtbo.img removed as it's not a valid target)
- ✅ No explicit `dtbs` build - following LineageOS approach
- ✅ Build errors are properly captured and displayed
- ✅ Device tree files remain in original proper state (overlay with `/plugin/`)

## Files Modified

1. `arch/arm64/configs/vendor/gta4xlve.config` - Added manual hook configuration
2. `kernelsu_hook_check.patch` - Patch to fix Kbuild hook check logic
3. `.github/workflows/build.yml` - Updated to apply patch, fix build targets, remove explicit dtbs build, and improve error handling
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
