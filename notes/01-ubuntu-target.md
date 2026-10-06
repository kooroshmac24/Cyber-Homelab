# 01 - Ubuntu Server target VM

**Date:** 6 October 2026
**Objective:** Build a target machine to attack from Kali, running Docker and Juice Shop, as the second VM in the homelab.

## Setup
- Host: MacBook Air M5, 16 GB RAM
- Hypervisor: UTM (QEMU backend)
- Guest: Ubuntu Server 26.04.1 LTS (arm64) from ubuntu.com
- VM resources: 2 GB RAM, 2 CPU cores, 25 GB virtual disk
- Network: Shared Network (temporary, for downloads and updates)

## Steps
1. Downloaded the Ubuntu Server ARM64 ISO from ubuntu.com/download/server/arm. Verified the SHA256 checksum in Mac Terminal.
2. Created a VM in UTM: Virtualize, Linux, Apple Virtualization unticked, 2 GB RAM, 2 cores, 25 GB disk.
3. Installed Ubuntu Server with the text-based installer:
   - Language: English, keyboard: British English
   - Hostname: `ubuntu-target`
   - Storage: used the entire virtual disk, expanded the LVM volume to use all available space
   - Ticked "Install OpenSSH server"
   - Skipped Ubuntu Pro and featured snaps
4. After install: shut down, cleared the ISO from the CD/DVD drive, booted and logged in.
5. Updated the system: `sudo apt update && sudo apt upgrade -y`.
6. Installed Docker: `sudo apt install docker.io -y`.
7. Added my user to the Docker group: `sudo usermod -aG docker $USER`, then logged out and back in.
8. Downloaded Juice Shop (not started yet): `docker pull bkimminich/juice-shop`.
9. Shut down and cloned the VM as `ubuntu-clean`.

## What I saw
Ubuntu boots to a text login prompt. No graphical desktop, which is normal for a server. Docker is installed and the Juice Shop image is stored locally.

## Problems and fixes

### 1. Forgotten password (again)
- **Symptom:** login incorrect after the first install.
- **What I tried:** booting into recovery mode via the UEFI menu, but couldn't reach the GRUB recovery menu. The UEFI firmware menu appeared instead.
- **Fix:** reinstalled Ubuntu from scratch with a simpler password (lowercase words with hyphens, no special characters). Saved the password in a password manager before starting the installer.
- **Lesson:** special characters like `@` and `#` can map differently between the installer and the login screen on a Mac VM. Use a long passphrase of plain words instead. Always save the password before setting it, not after.

### 2. `$variable` confusion with usermod
- **Symptom:** ran `sudo usermod -aG docker $gholiubuntu` and got a usage error.
- **Cause:** the `$` told the shell to look for a variable called `gholiubuntu`, which doesn't exist, so it sent nothing to the command.
- **Fix:** the earlier command `sudo usermod -aG docker $USER` had already worked. `$USER` is a built-in variable the system fills in automatically with the current username.
- **Lesson:** in Linux, `$` before a word means "the value of this variable." Use `$USER` for your username, or type the username without a `$`.

## How to prevent or detect it
Not applicable yet. This is infrastructure setup. Attack and detection write-ups start in Phase 4.

## Commands I used or looked up

| Command | What it does |
|---|---|
| `sudo apt update && sudo apt upgrade -y` | Refreshes the package list and installs all available updates |
| `sudo apt install docker.io -y` | Installs Docker from Ubuntu's package repository |
| `sudo usermod -aG docker $USER` | Adds the current user to the Docker group so Docker commands don't need sudo |
| `docker pull bkimminich/juice-shop` | Downloads the Juice Shop container image without starting it |
| `sudo poweroff` | Shuts the VM down cleanly |
| `exit` | Logs out of the current session (needed for group changes to take effect) |

## Lessons learned
- A long passphrase of plain words is both stronger and more reliable across different keyboard layouts than a short password full of symbols.
- `$USER` is a built-in shortcut for the current username. The `$` prefix means "variable," not "username."
- Downloading a Docker image and running it are separate steps. Downloading on a safe network and running on an isolated one is a good habit.
- Cloning the VM after setup but before running anything vulnerable gives a clean restore point.

## Still to do
- [ ] Switch both VMs to Host Only networking (Phase 3)
- [ ] Start Juice Shop on the isolated network
- [ ] Begin first attack from Kali
