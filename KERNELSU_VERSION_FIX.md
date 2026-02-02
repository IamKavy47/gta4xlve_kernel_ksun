# KernelSU Version Issue Fix - Using Legacy Branch

## Problem

After flashing the kernel, modules failed to install with error:
```
KernelSU version: 0 (kernel) + 32857 (ksud)
! KernelSU version is too old!
! Please update KernelSU to latest version
```

Root worked fine, but the kernel was reporting version **0** instead of the actual version.

## Root Cause

**Using wrong KernelSU-Next branch for kernel 4.14**

- Kernel version: **4.14** (old kernel, requires legacy support)
- Was using: **dev branch** (for modern kernels 5.10+)
- Should use: **legacy branch** (for kernels 4.4-5.4)

## Solution

### 1. Updated .gitmodules to use legacy branch
```
[submodule "KernelSU-Next"]
    path = KernelSU-Next
    url = https://github.com/KernelSU-Next/KernelSU-Next.git
    branch = legacy  ← Added this
```

### 2. Updated workflow to explicitly checkout legacy
The workflow now:
- Explicitly checks out the **legacy** branch
- Pulls latest changes from legacy
- Displays version information clearly
- Creates proper .git symlinks for version detection

### 3. Version Detection
With legacy branch:
- Commit count: ~2896
- Calculated version: **32956** (30000 + 2896 + 60)
- Tag: v3.0.0
- This version is valid and modules will install!

## Why This Matters

KernelSU-Next has different branches for different kernel versions:

| Branch | Kernel Version | Use Case |
|--------|----------------|----------|
| **legacy** | 4.4 - 5.4 | Old kernels (like 4.14) |
| dev | 5.10+ | Modern kernels |
| stable | Latest stable | Production use |
| next-susfs-a13-5.15-dev | 5.15 | Android 13 specific |
| next-susfs-a14-6.1-dev | 6.1 | Android 14 specific |

**Using the wrong branch causes**:
- Incompatible manual hooks
- Version detection failures
- Module installation errors
- Even if root works, modules see "version too old"

## For Kernel 4.14 Users

If you're building for kernel 4.14 (or any 4.x/early 5.x kernel):

1. **Always use the legacy branch**
2. Update your .gitmodules:
   ```bash
   git config -f .gitmodules submodule.KernelSU-Next.branch legacy
   git submodule update --remote
   ```

3. Or manually:
   ```bash
   cd KernelSU-Next
   git checkout legacy
   git pull origin legacy
   ```

## Verification

After this fix, the build will show:
```
==========================================
KernelSU-Next Configuration (Legacy Branch for 4.14)
==========================================
Branch: legacy (for kernel 4.14)
Commit Count: 2896
Calculated Version: 32956
Git Tag: v3.0.0
==========================================
```

And module installation will succeed:
```
KernelSU version: 32956 (kernel) + 32857 (ksud)
✓ Module installed successfully
```

## Which Manager App to Install?

**IMPORTANT**: You need **KernelSU-Next Manager**, NOT the original KernelSU Manager!

### Quick Answer
- **Download**: https://github.com/rifsxd/KernelSU-Next/releases
- **Get**: Latest APK (any v1.x.x version works with legacy branch)
- **Don't use**: Original KernelSU Manager from tiann (incompatible)

### Full Details
See **MANAGER_VERSION.md** for:
- Detailed installation instructions
- Version compatibility info
- Troubleshooting manager issues
- Module compatibility

## References

- KernelSU-Next legacy branch: https://github.com/KernelSU-Next/KernelSU-Next/tree/legacy
- KernelSU-Next Manager: https://github.com/rifsxd/KernelSU-Next/releases
- Kernel 4.14 compatibility: Requires legacy branch with scope minimized hooks
- Manual hooks 1.7: Included in legacy branch for older kernel compatibility
- Manager guide: See MANAGER_VERSION.md
