# Ansible Role: `packages`

📦 **Install and configure essential packages, LazyGit, LazyDocker, and Fail2Ban on Debian-based systems.**

---

## Overview

This Ansible role automates the installation and basic configuration of:

- Common utility packages (e.g. `tmux`, `git`, `fzf`, `bat`, etc.)
- [LazyGit](https://github.com/jesseduffield/lazygit) via the official GitHub release archive  
- [LazyDocker](https://github.com/jesseduffield/lazydocker) via the official install script  
- [Fail2Ban](https://www.fail2ban.org/) with a sensible default `jail.local`  

The role ensures tools are available system-wide or user-accessible, and sets up shell environment paths as needed.

---

## Role Features

✅ Installs utility tools using `apt`  
✅ Installs and configures **Fail2Ban**  
- Drops in a default `/etc/fail2ban/jail.local` with SSH, Samba and recidive jails  
- Enables and starts the `fail2ban` service  
✅ Downloads and installs the latest LazyGit binary  
✅ Configures Git’s default branch to `main`  
✅ Installs LazyDocker and adds it to the user’s `$PATH`  
✅ Fully idempotent and supports `arm64`, `x86_64`, and Raspberry Pi OS  

---

## Requirements

- Ansible ≥ 2.9  
- Target system must support `apt` (e.g., Debian, Ubuntu, Raspberry Pi OS)  
- A working MTA or `msmtp` configured as `/usr/sbin/sendmail` if you wish to receive Fail2Ban email alerts  

---

## Variables

### Default Variables (`defaults/main.yml`)

```yaml
packages_to_install:
  - bat
  - btop
  - cron
  - fail2ban
  - fzf
  - git
  - lsd
  - ncdu
  - neofetch
  - ranger
  - tmux
  - tldr
  - tree
  - zoxide
  - zsh

# Email address to receive Fail2Ban alerts (templated into jail.local)
destemail: "you@example.com"
```

> **Override** any of these to customize the packages installed or the alert recipient.

---

## LazyGit

Automatically:
- Detects system architecture (`arm64`, `x86_64`, `armv6`)  
- Downloads the latest GitHub release  
- Extracts and installs it to `/usr/local/bin`  
- Cleans up temp files  
- Configures Git’s default branch name to `main`  

---

## LazyDocker

- Checks if it’s already installed in `~/.local/bin`  
- Uses the official `install_update_linux.sh` script  
- Adds `~/.local/bin` to `~/.zshrc` if necessary  

---

## Fail2Ban

- Installs the `fail2ban` package  
- Deploys a default `/etc/fail2ban/jail.local` that includes:
  - **SSH** jail (systemd backend)  
  - **Samba** jail  
  - **Recidive** jail for repeat offenders  
- Templates in your `destemail` for email alerts  
- Enables and starts the `fail2ban` service  

---

## Usage Example

```yaml
- name: Setup dev tools and protection
  hosts: testpi
  roles:
    - packages
```

This will install your chosen utilities, set up LazyGit/LazyDocker, and bring up Fail2Ban with alerts sent to `admin@mydomain.com`.

---

## License

MIT

---

## Author

Vantasin
