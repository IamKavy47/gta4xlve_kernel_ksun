# KernelSU Build Fix

## Problem

The kernel build was failing with the following error:
```
../drivers/kernelsu/Kbuild:110: *** -- KernelSU: No hooks were defined, please integrate manual hooks in your kernel!.  Stop.
```

## Root Cause

The issue had two parts:

1. **Missing Configuration**: The `CONFIG_KSU_MANUAL_HOOK` option was not explicitly enabled in `arch/arm64/configs/vendor/gta4xlve.config`, even though manual hooks were already integrated in the kernel source files.

2. **Build System Issue**: The KernelSU Kbuild file was checking for hooks even during `make clean` and `make mrproper` operations, before the kernel configuration was loaded. This caused the check to fail because `CONFIG_KSU_MANUAL_HOOK` was undefined during clean operations.

## Manual Hooks Present

The kernel already has the following KernelSU manual hooks integrated:
- `fs/open.c`: `ksu_handle_faccessat`
- `fs/exec.c`: `ksu_handle_execveat`, `ksu_handle_execveat_sucompat`
- `fs/read_write.c`: `ksu_handle_vfs_read`
- `fs/stat.c`: `ksu_handle_stat`

## Solution

### 1. Configuration Fix
Added the following lines to `arch/arm64/configs/vendor/gta4xlve.config`:
```
CONFIG_KSU_MANUAL_HOOK=y
# CONFIG_KSU_KPROBES_HOOK is not set
```

### 2. Build System Fix
Created a patch file `kernelsu_hook_check.patch` that wraps the hook check in the KernelSU Kbuild file with a condition:
```makefile
ifeq ($(CONFIG_KSU), y)
# ... hook check logic ...
endif
```

This ensures the hook check only runs when KSU is actually being built, not during clean operations.

### 3. Workflow Integration
Updated `.github/workflows/build.yml` to automatically apply the patch during the KernelSU setup step.

## Verification

After applying these fixes:
- ✅ `make O=out clean` completes without errors
- ✅ `make O=out mrproper` works correctly
- ✅ Config merging properly sets `CONFIG_KSU_MANUAL_HOOK=y`
- ✅ KernelSU build shows "-- KernelSU: Hook mode: Manual" confirming hooks are detected

## Files Modified

1. `arch/arm64/configs/vendor/gta4xlve.config` - Added manual hook configuration
2. `kernelsu_hook_check.patch` - Patch to fix Kbuild hook check logic
3. `.github/workflows/build.yml` - Updated to apply the patch during setup
4. `KERNELSU_FIX.md` - This documentation file
