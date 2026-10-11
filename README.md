# Cybersecurity Homelab

A beginner homelab for practising attack and detection, built on a MacBook Air (Apple Silicon) with UTM. I'm documenting every step as I learn.

## Goal

Build a small, isolated lab where I can attack machines I own, see what those attacks look like from the defender's side, and write up what I learn.

## Lab Machines

| VM | Role | OS | IP Address | RAM |
|----|------|----|------------|-----|
| kali | Attacker | Kali Linux (ARM64) | 192.168.128.2 | 3GB |
| ubuntu-target | Target | Ubuntu Server 26.04.1 LTS (ARM64) | 192.168.128.4 | 2GB |
| kali-clean | Backup clone | Kali Linux (ARM64) | — | — |
| ubuntu-clean | Backup clone | Ubuntu Server (ARM64) | — | — |

**Network:** Host Only (isolated — no internet while attacks are running)  
**Vulnerable App:** OWASP Juice Shop (Docker container on Ubuntu, port 3000)

## Write-ups

- [00 — Setup log: Kali attacker VM](notes/00-setup-log.md)
- [01 — Ubuntu Server target VM](notes/01-ubuntu-target.md)
- [02 — Juice Shop attacks: Nmap, SQL injection, XSS](notes/02-juice-shop-attacks.md)

## Status

- [x] Kali VM installed
- [x] Ubuntu Server target with Docker and Juice Shop
- [x] Isolated Host Only network verified (ping google.com fails on both VMs)
- [x] VMs can communicate (Kali → Ubuntu ping: 3/3 packets, 0% loss)
- [x] Nmap reconnaissance — found 2 open ports (SSH on 22, Juice Shop on 3000)
- [x] SQL injection — bypassed admin login with `' OR 1=1--`
- [x] XSS — injected and executed JavaScript via search bar
- [ ] SSH brute-force with Hydra
- [ ] Blue team — read logs to detect attacks
- [ ] Network diagram
- [ ] Wazuh (defender)

## Tools Used

- **UTM** — virtualisation on Apple Silicon (QEMU backend)
- **Kali Linux** — attacker OS with pre-installed security tools
- **Ubuntu Server** — target OS running vulnerable applications
- **Docker** — containerised deployment of Juice Shop
- **Nmap** — network scanning and service detection
- **OWASP Juice Shop** — deliberately vulnerable web application

## Repo Structure

| Folder | What's in it |
|--------|-------------|
| `notes/` | Numbered write-ups, one per exercise, plus a blank template |
| `screenshots/` | Images used in the write-ups |
| `diagrams/` | Network diagrams of the lab |
| `configs/` | Sanitised config files and detection rules (no passwords or tokens) |

## Rules I Follow

I only attack machines I built in this lab. The lab network is isolated from my home and university networks. Updates are only done after switching to Shared Network (NAT) with no vulnerable services running.
