# 🐧 linux-kernel

**Collection of information, builds, tools, and everything related to my Linux kernel development workflow.**

This repository contains:

* My kernel builds (organized by version)
* Kernel configs
* Tools and scripts for building & testing kernels
* Development notes, patches, and experiments
* QEMU launch commands for booting custom kernels

The goal of this repo is to keep a clean, reproducible workspace for learning Linux kernel development, testing patches, and experimenting safely inside virtual machines.

---

## Booting a Custom Kernel in QEMU/KVM

You can boot any compiled kernel image stored in `builds/` using QEMU.

Example command (for build **v0**):

```bash
sudo qemu-system-x86_64 \
  -enable-kvm \
  -m 4G \
  -smp 4 \
  -kernel ~/linux/builds/v0/bzImage \
  -append "root=/dev/vda2 rw console=ttyS0 loglevel=7" \
  -drive file=/var/lib/libvirt/images/archlinux.qcow2,format=qcow2 \
  -serial mon:stdio
```

### What this command does:

* **-kernel** points to the custom kernel you compiled
* **root=/dev/vda2** boots the VM using the Arch Linux disk’s root partition
* **console=ttyS0** sends boot logs to your terminal
* **-enable-kvm** enables hardware acceleration
* **-drive** attaches your existing VM disk
* **loglevel=7** prints maximum debugging output

---

## Building the Kernel

Inside your kernel source directory (`linux/`):

1. Configure the kernel:

   ```bash
   make menuconfig
   ```

   or import host config:

   ```bash
   zcat /proc/config.gz > .config
   make olddefconfig
   ```

2. Compile:

   ```bash
   make -j$(nproc)
   ```

3. Your important outputs will be:

   * `arch/x86/boot/bzImage`
   * `System.map`
   * `.config`

Copy them into a new version directory:

```
builds/vX/
├── bzImage
├── System.map
└── config
```

Replace `vX` with the version number (v1, v2, etc.).

---

## Updating the Repository with New Builds

Each time you build a new kernel:

1. Create a new build directory:

   ```
   builds/v1/
   ```
2. Copy over:

   * `bzImage`
   * `System.map`
   * `.config`
3. Commit:

   ```bash
   git add builds/v1
   git commit -m "Add kernel build v1"
   git push
   ```

This approach preserves a full history of experiments and allows you to boot any prior build.

---

## Repository Structure

```
linux-kernel/
│
├── builds/          # Compiled kernel builds organized by version
├── configs/         # Saved kernel configs
├── patches/         # format-patch outputs
├── scripts/         # build + QEMU run scripts
└── README.md
```
✔ patch workflow docs

Just tell me **“generate scripts”** or **“add more sections”**.
