# 00 - Setup log: Kali Linux attacker VM

**Date:** 04/10/2026
**Objective:** Get a working Kali Linux VM on Apple Silicon to use as the attacker machine in my homelab.
**Host machine:** MacBook Air M5, 16GB RAM, 512GB storage
**Hypervisor:** UTM (QEMU backend), version Version 4.7.5 (118)
**Starting knowledge:** Google Cybersecurity Certificate, basic Linux CLI (rusty)

## Setup
- Host: MacBook Air M5 (ARM64)
- Hypervisor: UTM
- Guest: Kali Linux, arm64 installer ISO from kali.org
- VM resources: 4 GB RAM, (2-4) CPU cores, 64 GB virtual disk

## Steps
1. Created the GitHub repo and set up the README and `notes/` folder.
2. Installed UTM from mac.getutm.app.
3. Downloaded the Kali **arm64** installer ISO from kali.org and checked the filename contained `arm64`. Checksum verified: yes.
4. Created a VM in UTM: **Virtualize**, OS type **Other** (as in Kali's UTM guide), the ISO as boot image, 4 GB RAM, 64 GB disk.
5. Added a **Serial** device to the VM (Settings > Devices > New), then ran the installer from the Serial window (see Problems below).
6. Installed Kali with the text installer:
   - hostname `kali`, domain left blank
   - a non-root user account
   - guided partitioning, entire virtual disk, all files in one partition
   - default software selection (Xfce desktop and the default Kali tools)
   - GRUB installed on the virtual disk
7. After the install, shut the VM down and in its settings: cleared the ISO from the CD/DVD drive, removed the Serial device, and set the display card to `virtio-gpu-pci`.
8. Booted into Kali and logged in.

## What I saw
Kali boots to the Xfce login screen and desktop.

<img width="2554" height="1672" alt="image" src="https://github.com/user-attachments/assets/e5aff719-38d0-4847-8ac5-3552d04ff48d" />


## Problems and fixes

### 1. Black screen with a blinking cursor in the installer
- **Symptom:** Graphical install and the text installer both showed a black screen with a blinking `_`. Clicking Install gave "display output not active".
- **First check:** the ISO filename contained `arm64`, so it wasn't an Intel/ARM mismatch.
- **Cause:** Kali's UTM documentation says the installer has to be run in console-only mode on UTM, which needs a Serial device added to the VM.
- **Fix:** recreated the VM, added a Serial device, and used the Serial window (not the display window) to run the text installer.
- **Lesson:** a black display doesn't always mean the installer failed. It can be sending output to a different console. Check the official docs for your exact hypervisor early.

### 2. Confusion over which download to use
- The kali.org ARM section lists images for physical boards (Raspberry Pi, Pinebook and similar), which don't apply to a UTM VM. The right download was the Apple Silicon (ARM64) installer ISO, under Installer Images.
- Kali's docs list pre-made VMs for VMware, VirtualBox and Hyper-V, not UTM, so I used the ISO route.
- **Lesson:** read the section name on a download page, and confirm a download option exists on the official site before planning around it.

## Commands I used or looked up

| Command | What it does |
|---|---|
| `shasum -a 256 <file>` | Verifies a download's checksum (run on the Mac) |

## Lessons learned
- Troubleshooting is a big part of the lab, so writing down what broke is as useful as writing down what worked.
- Official documentation beat generic tutorials for the black screen problem.
- Check the details of a download (architecture, section) before spending time on it.

## Still to do
- [x] Update Kali: `sudo apt update && sudo apt full-upgrade -y`
- [x] Shut down and clone the VM as `kali-clean` (rescue copy)
- [ ] Build the Ubuntu Server target VM
- [ ] Set up the isolated host-only network

- Completed shut down of the sudo apt update, and made a Kali linux clone that will serve as my rescue copy.
<img width="1564" height="1684" alt="image" src="https://github.com/user-attachments/assets/d53cb6e4-ae12-41f4-8402-5058e404e999" />

