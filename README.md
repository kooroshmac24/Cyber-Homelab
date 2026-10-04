# Cyber-Homelab
# Setup Log

**Date:** 04/10/2026
**Goal:** Build an isolated cybersecurity homelab to practise attack and detection, and document it for my portfolio.
**Host machine:** MacBook Air M5, 16GB RAM, 512 storage
**Starting knowledge:** Google Cybersecurity Certificate, basic Linux CLI (rusty)

## What I did
- Created the GitHub repo
- ## Lab machines

| VM | Role | OS | IP address |
|----|------|----|------------|
| kali | Attacker | Kali Linux (ARM64) | TBD |

# Setup Log: Kali Linux attacker VM

**Date:** 04/10/2026
**Goal:** Build an isolated cybersecurity homelab to practise attack and detection, and document it for my portfolio.
**Host machine:** MacBook Air M5, 16GB RAM, 512 storage
**Hypervisor:** UTM (QEMU backend), version (check UTM > About UTM)
**Starting knowledge:** Google Cybersecurity Certificate, basic Linux CLI (rusty)

## Objective
Get a working Kali Linux VM on Apple Silicon to use as the attacker machine in the lab.

## What I did
1. Created this GitHub repo and set up the README and notes folder.
2. Installed UTM from mac.getutm.app.
3. Downloaded the Kali **arm64** installer ISO from kali.org. Checksum verified: yes 
4. Created a VM in UTM using Virtualize, 4 GB RAM, 2 CPU cores, 64 GB disk.
5. Installed Kali using the text installer: guided partitioning on the entire virtual disk, default software selection, hostname `kali`.
6. After install: shut down, cleared the ISO from the CD/DVD drive, removed the Serial device, set the display card to `virtio-gpu-pci`.
7. Booted into Kali and logged in.

<img width="2554" height="1672" alt="image" src="https://github.com/user-attachments/assets/d7fbc1c3-ba94-42ac-ba18-56b0f463653b" />

## Problems and how I fixed them

### 1. Black screen with a blinking cursor in the installer
- **Symptom:** Both Graphical install and the text installer showed a black screen with a blinking `_`. Clicking Install gave "display output not active".
- **What I checked first:** the ISO filename was `arm64` (right architecture), so it wasn't an Intel/ARM mismatch.
- **Cause:** per Kali's own UTM documentation, the installer has to be run in console-only mode on UTM, which needs a Serial device added to the VM.
- **Fix:** recreated the VM, added a **Serial** device under Devices, then used the Serial window (not the display window) to run the text installer.
- **Lesson:** a black display doesn't mean the installer has failed. It can be sending output to a different console. Check the official docs for your exact hypervisor early.


### 2. Looking for a pre-built image
- I searched for a pre-built Apple Silicon image to skip the installer. Kali's docs list pre-made VMs for VMware, VirtualBox and Hyper-V, but none for UTM, so the ISO route was the right one.
- **Lesson:** verify a download option exists on the official site before planning around it.

### 3. Forgotten password
- **Symptom:** couldn't log in right after the first boot.
- **Fix:** booted into GRUB recovery mode, dropped to a root shell, remounted the disk read-write with `mount -o remount,rw /`, and reset the password with `passwd <username>`.
- **Lesson:** anyone with console access to a VM can reset a Linux password this way, which is why physical access matters in security. Saved the new password in a password manager.

## Commands I used or looked up
| Command | What it does |
|---|---|
| `shasum -a 256 <file>` | Verify a download's checksum (on the Mac) |
| `sudo apt update && sudo apt full-upgrade -y` | Refresh and install updates |
| `mount -o remount,rw /` | Make the root filesystem writable in recovery mode |
| `passwd <user>` | Change a user's password |

## Next steps
- Update Kali and clone it as `kali-clean`.
- Build the Ubuntu Server target VM with Docker and Juice Shop.
- Set up the isolated host-only network.
