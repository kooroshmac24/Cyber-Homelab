# Cyber-Homelab
# Setup Log

**Date:** 04/10/2026
**Goal:** Build an isolated cybersecurity homelab to practise attack and detection, and document it for my portfolio.
**Host machine:** MacBook Air M5, 16GB RAM, 512 storage
**Starting knowledge:** Google Cybersecurity Certificate, basic Linux CLI (rusty)

# Cybersecurity Homelab

A beginner homelab for practising attack and detection, built on a MacBook Air (Apple Silicon) with UTM. I'm documenting every step as I learn.

## Goal
Build a small, isolated lab where I can attack machines I own, see what those attacks look like from the defender's side, and write up what I learn.

## Lab machines

| VM | Role | OS | IP address |
|----|------|----|------------|
| kali | Attacker | Kali Linux (ARM64) | TBD |

## Write-ups
- [00 - Setup log: Kali attacker VM](notes/00-setup-log.md)
- [01 - Ubuntu Server target VM](notes/01-ubuntu-target.md)
## Status
- [x] Kali VM installed
- [x] Ubuntu Server target with Docker and Juice Shop
- [ ] Isolated host-only network
- [ ] First attack and write-up
- [ ] Wazuh (defender)

## Repo structure

| Folder | What's in it |
|---|---|
| `notes/` | Numbered write-ups, one per exercise, plus a blank template |
| `screenshots/` | Images used in the write-ups |
| `diagrams/` | Network diagrams of the lab |
| `configs/` | Sanitised config files and detection rules (no passwords or tokens) |


## Rules I follow
I only attack machines I built in this lab. The lab network is isolated from my home and university networks.
