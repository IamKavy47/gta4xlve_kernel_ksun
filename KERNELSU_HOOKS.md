# KernelSU Manual Hooks - Complete Integration

## Overview

This kernel uses **KernelSU-Next legacy branch** with **manual hooks** integration. Manual hooks are required for kernel 4.14 because kprobes support is limited on older kernels.

## Hook Check Requirement

The KernelSU-Next legacy branch Kbuild checks for the presence of `ksu_handle_sys_reboot` hook:

```makefile
# From KernelSU-Next/kernel/Kbuild line 104
ifeq ($(CONFIG_KSU_MANUAL_HOOK), y)
HAVE_KSU_HOOK := $(shell grep -q "ksu_handle_sys_reboot" $(srctree)/kernel/reboot.c && echo 0 || echo 1)
ifeq ($(HAVE_KSU_HOOK),0)
$(info -- KernelSU-Next: Hook mode: Manual)
endif
endif

ifneq ($(HAVE_KSU_HOOK),0)
$(error -- KernelSU-Next: No hooks were defined, please integrate manual hooks in your kernel!)
endif
```

## All Manual Hooks Integrated

### 1. kernel/reboot.c - System Reboot Hook
**Purpose**: Intercepts system reboot calls for KernelSU management

```c
extern int ksu_handle_sys_reboot(int *magic1, int *magic2, unsigned int *cmd,
				 void __user **arg);

SYSCALL_DEFINE4(reboot, int, magic1, int, magic2, unsigned int, cmd,
		void __user *, arg)
{
	// ... 
	ksu_handle_sys_reboot(&magic1, &magic2, &cmd, &arg);
	// ...
}
```

**Required for**: Build verification (checked by Kbuild)

### 2. fs/exec.c - Exec Hooks
**Purpose**: Intercepts program execution for root management

```c
extern int ksu_handle_execveat(int *fd, struct filename **filename_ptr, void *argv,
			       void *envp, int *flags);
extern int ksu_handle_execveat_sucompat(int *fd, struct filename **filename_ptr,
					void *argv, void *envp, int *flags);

// Called in do_execveat_common()
ksu_handle_execveat(&fd, &filename, &argv, &envp, &flags);
ksu_handle_execveat_sucompat(&fd, &filename, &argv, &envp, &flags);
```

**Required for**: Root permission management, su compatibility

### 3. fs/open.c - File Access Hook
**Purpose**: Intercepts file access checks

```c
extern int ksu_handle_faccessat(int *dfd, const char __user **filename_user, int *mode,
			        int *flags);

// Called in SYSCALL_DEFINE3(faccessat)
ksu_handle_faccessat(&dfd, &filename, &mode, NULL);
```

**Required for**: Access control and permission checks

### 4. fs/read_write.c - VFS Read Hook
**Purpose**: Intercepts VFS read operations

```c
extern int ksu_handle_vfs_read(struct file **file_ptr, char __user **buf_ptr,
			       size_t *count_ptr, loff_t **pos_ptr);

// Called in vfs_read()
ksu_handle_vfs_read(&file, &buf, &count, &pos);
```

**Required for**: Monitoring file reads, security checks

### 5. fs/stat.c - Stat Hook
**Purpose**: Intercepts file stat operations

```c
extern int ksu_handle_stat(int *dfd, const char __user **filename_user, int *flags);

// Called in vfs_statx()
ksu_handle_stat(&dfd, &filename, &flags);
```

**Required for**: File metadata access control

### 6. fs/devpts/inode.c - Devpts Hook  
**Purpose**: Intercepts devpts inode operations

```c
extern int ksu_handle_devpts(struct inode*);

// Called in devpts operations
```

**Required for**: PTY/terminal device management

## Configuration

In `arch/arm64/configs/vendor/gta4xlve.config`:

```makefile
CONFIG_KSU=y
CONFIG_KSU_MANUAL_HOOK=y      # Enable manual hooks
# CONFIG_KSU_KPROBES_HOOK is not set  # Disable kprobes (not available on 4.14)
```

## Why Manual Hooks for Kernel 4.14?

### Legacy Kernel Limitations

| Feature | Kernel 4.14 | Kernel 5.10+ |
|---------|-------------|--------------|
| Kprobes support | Limited/unstable | Full support |
| Hook method | Manual integration | Kprobes or manual |
| Branch | legacy | dev |

### Manual vs Kprobes Hooks

**Manual Hooks** (used in this kernel):
- ✅ Direct integration into kernel source
- ✅ Stable on older kernels
- ✅ No runtime overhead
- ❌ Requires kernel source modifications
- ❌ More maintenance needed

**Kprobes Hooks** (newer kernels):
- ✅ No kernel source modifications needed
- ✅ Dynamic hooking
- ❌ Requires CONFIG_KPROBES support
- ❌ Not stable on kernel 4.14

## Build Verification

During build, you should see:

```
-- KernelSU-Next Git repo detected at: /path/to/KernelSU-Next
-- KernelSU-Next version: 32973
-- KernelSU-Next tag: v3.0.0
-- KernelSU-Next: Hook mode: Manual
✓ KernelSU-Next (legacy branch) linked and patched
✓ Version: 32973 (v3.0.0)
```

## Troubleshooting

### "No hooks were defined" Error

**Symptoms**:
```
-- KernelSU-Next: No hooks were defined, please integrate manual hooks in your kernel!
make[2]: *** [../scripts/Makefile.build:679: drivers/kernelsu] Error 2
```

**Causes**:
1. ❌ `ksu_handle_sys_reboot` hook missing from kernel/reboot.c
2. ❌ `CONFIG_KSU_MANUAL_HOOK` not set to `y`
3. ❌ Hook check patch not applied

**Solutions**:
1. ✅ Ensure all hooks are integrated (especially sys_reboot)
2. ✅ Verify `CONFIG_KSU_MANUAL_HOOK=y` in config
3. ✅ Apply kernelsu_hook_check.patch before build

### Verifying Hooks

Check all hooks are present:

```bash
# Check sys_reboot hook (required for build)
grep "ksu_handle_sys_reboot" kernel/reboot.c

# Check other hooks
grep "ksu_handle_execveat" fs/exec.c
grep "ksu_handle_faccessat" fs/open.c
grep "ksu_handle_vfs_read" fs/read_write.c
grep "ksu_handle_stat" fs/stat.c
grep "ksu_handle_devpts" fs/devpts/inode.c
```

All should return results!

## References

- KernelSU-Next Legacy Branch: https://github.com/KernelSU-Next/KernelSU-Next/tree/legacy
- Manual Hooks Documentation: Scope minimized hooks 1.7
- Kernel Version: 4.14 (requires legacy branch)
- KernelSU Version: 32973+ (from legacy branch)

## Summary

✅ **6 manual hooks integrated**  
✅ **CONFIG_KSU_MANUAL_HOOK=y** configured  
✅ **sys_reboot hook** added (build verification passes)  
✅ **Legacy branch** for kernel 4.14 compatibility  
✅ **Version 32973** properly detected  

All requirements met for successful KernelSU-Next legacy build!
