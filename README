# Android Custom Kernel for Docker for Xiaomi Mi 11 Lite 4G

This repository is a fully configured custom Android Kernel. It features out-of-the-box 
support for running **Docker containers natively on Android** by integrating the necessary 
kernel namespaces, cgroups, network virtualization drivers, and custom upstream compilation fixes.

Android kernels typically strip out containerization subsystems to save memory and minimize attack surfaces. This project restores those features, addresses breaking code segments in the scheduler/PSI structures, and provides an automated environment to compile and package flashable kernel ZIPs.

Inspired by [FreddieOliveira's Docker-Android Guide](https://gist.github.com/FreddieOliveira/efe850df7ff3951cb62d74bd770dce27).

---

## 🚀 Key Enhancements

- **Docker Support:** Merges all core prerequisites (`CONFIG_NAMESPACES`, `CONFIG_CGROUPS`, 
`CONFIG_OVERLAY_FS`, `CONFIG_VETH`, `CONFIG_BRIDGE`, etc.) directly into the configuration 
cycle.
- **Embedded Source Patches:** Upstream compilation blocks inside `kernel/sched/fair.c` and `kernel/sched/psi.c` have been pre-patched to prevent errors caused by tracing and scheduling limits.
- **Automated Deployment:** Integrates with AnyKernel3 to instantly output flashable recovery ZIPs targeted to your device's specific hardware codename.

---

## 🛠️ Custom Modifications 

### 1. Unified Configuration Merging (`docker_fix.config`)
A curated configuration fragment is tracked in the root directory. During the compilation 
process, this file is automatically merged with the device's base `defconfig` to toggle all 
systems needed by the Docker Engine:

```ini
# Core Container Isolation
CONFIG_NAMESPACES=y
CONFIG_USER_NS=y

# Resource Accounting & Constraints
CONFIG_CGROUPS=y
CONFIG_MEMCG=y
CONFIG_MEMCG_SWAP=y
CONFIG_MEMCG_SWAP_ENABLED=y
CONFIG_MEMCG_V1=y
CONFIG_CFS_BANDWIDTH=y

# Storage Drivers
CONFIG_OVERLAY_FS=y

# Advanced Virtual Networking
CONFIG_VETH=y
CONFIG_BRIDGE=y
...etc
```

### 2. Embedded Structural Patches
Enabling extensive resource tracking exposed breaking changes in the base LineageOS SM6150 platform code. This fork includes pre-applied commits for:
* `kernel/sched/fair.c`: Fixes broken macro expansions for Completely Fair Scheduler (CFS) 
load averaging constraints when handling complex thread domains.

* `kernel/sched/psi.c`: Fixes reference-counting pointer mismatches within Pressure Stall 
Information trackers.

> 💡 To be fair, I just commented out the problematic parts lol.

## ⚡ Building From Source
### Prerequisites
1. Ensure your host system provides a valid cross-compilation environment.
2. Ensure you have the Neutron Clang toolchain pulled to your home directory exactly as 
mapped below (modify the build script, build.sh as appropriate):
```bash
/home/$USER/toolchains/neutron-clang/
```

### Step-by-Step Compilation
1. Clone this repository directly onto your build machine:
```bash
git clone https://github.com/posilash/xiaomi_sm6150_kernel.git
cd xiaomi_sm6150_kernel
```
2. Run the build script:
```bash
./build.sh
```
*The script will prompt you to enter one of the allowed target device codenames (courbet).*

## 📦 Flashing and Verification
### AnyKernel3 Package Output
Once compilation is done, the script automatically tracks down the compiled binaries 
(`Image.gz`, `dtbo.img`, `dtb.img`), auto-clones the AnyKernel3 deployment template, injects 
the device signature configuration, and packs it up:
```
📦 Target Output: [codename]-YYYYMMDD-HHMM.zip
```
Flash the generated ZIP using a custom recovery environment (such as TWRP or OrangeFox) or 
run it directly through root utility apps like KernelFlasher.

## Verifying Docker Compatibility
Once booted back into your system, check if the kernel successfully exposes everything 
Docker expects:
1. Fire up an ADB shell or a local terminal emulator with root privileges (su).
2. Download and run the official engine verification script:
```bash
curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh | bash
```
*Most critical parameters under Namespaces, Cgroups, and Storage should now display a green 
`Generally Necessary: enabled` status.*

# 🤝 Credits
- [meloalfa159](https://github.com/meloalfa159) for the base SM6150 kernel source.

- [FreddieOliveira](https://gist.github.com/FreddieOliveira) for the detailed guide.

- [osm0sis](https://github.com/osm0sis) for the AnyKernel3 template.
