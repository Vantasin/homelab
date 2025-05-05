# 🕒 Ansible Role: `cron`
[![Ansible Role](https://img.shields.io/badge/Ansible-Role-blue?logo=ansible)](https://docs.ansible.com/)  
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)  
[![Status: Stable](https://img.shields.io/badge/status-stable-brightgreen)](https://github.com/)  
[![Platform: Raspberry Pi](https://img.shields.io/badge/platform-Raspberry%20Pi-red)](https://www.raspberrypi.com/)

---

## 📚 Table of Contents
- [📦 Features](#-features)
- [🧱 Role Structure](#-role-structure)
- [⚙️ Variables](#️-variables)
- [⏲️ Installed Cron Jobs](#️-installed-cron-jobs)
- [📬 Email Reporting](#-email-reporting)
- [🔁 Log Rotation](#-log-rotation)
- [🔄 Flow Chart](#-flow-chart)
- [✅ Usage](#-usage)

---

## 📦 Features
- ✅ Deploys modular Bash scripts and wrappers via Jinja2 templates  
- ✅ Email notifications on **failure** for critical jobs (via `msmtp`)  
- ✅ Rotated logs with `logrotate` using `dateext` naming  
- ✅ ZFS scrub scheduled monthly for pool integrity  
- ✅ Designed for **automation**, **idempotency**, and **extensibility**

---

## 🧱 Role Structure

```
roles/cron/
├── defaults/
│   └── main.yml
├── tasks/
│   ├── prepare.yml
│   ├── reboot_check.yml
│   ├── zfs_scrub.yml
│   ├── setup_scripts.yml
│   ├── logrotate.yml
│   └── main.yml
├── templates/
│   ├── reboot_check.sh.j2
│   ├── zfs_scrub.sh.j2
│   └── cron_logrotate.conf.j2
├── meta/
│   └── main.yml
├── handlers/
├── files/
└── README.md
```

---

## ⚙️ Variables

| Name                  | Default                       | Description                                                 |
|-----------------------|-------------------------------|-------------------------------------------------------------|
| `cron_scripts_path`   | `/usr/local/bin/cron`         | Directory where all cron-wrapper scripts are deployed      |
| `cron_log_path`       | `/var/log/cron`               | Base directory for logs                                    |
| `msmtp_config_path`   | `/etc/msmtprc`                | Path to your `msmtp` configuration                         |
| `admin_email`         | _(must override)_             | Email address to receive alerts                            |
| `zfs_pool_name`       | `tank` _(recommended)_        | Name of your ZFS pool (used in `sanoid.conf.j2`, scrub)    |

---

## ⏲️ Installed Cron Jobs

| Job                       | Schedule                   | Script                         | Log Location                   | Rotation           |
|---------------------------|----------------------------|--------------------------------|--------------------------------|--------------------|
| **Reboot Check**          | Daily @ 02:30              | `reboot_check.sh`              | `system/reboot_check.log`      | Daily × 30 days    |
| **ZFS Scrub**             | Monthly @ 04:00 (day 1)    | `zfs_scrub.sh`                 | `zfs/zfs_scrub.log`            | Monthly × 12 months|

---

## 📬 Email Reporting
- **Scrub** script send a summary email _every_ time it runs.
- **Reboot** runs silently and only sends an email **if** a reboot is required..

---

## 🔁 Log Rotation
Managed via `/etc/logrotate.d/cron-custom` (from `cron_logrotate.conf.j2`):

```text
/var/log/cron/reboot_check.log
{
    daily
    rotate 30
    compress
    missingok
    notifempty
    copytruncate
    dateext
    dateformat -%Y-%m-%d
}

/var/log/cron/zfs_scrub.log
{
    monthly
    rotate 12
    compress
    missingok
    notifempty
    copytruncate
    dateext
    dateformat -%Y-%m
}
```

---

## 🔄 Flow Chart

```text
                   ┌────────────────────────────┐
                   │     Systemd / Cron Daemon  │
                   └────────────┬───────────────┘
                                │
                ┌───────────────▼────────────────┐
                │ Scheduled times (daily/monthly) │
                └───────────────┬────────────────┘
                                ▼
         ┌─────────────────────────────────────────┐
         │ /usr/local/bin/cron/*.sh (templates)   │
         └───────────────┬────────────────────────┘
                         │
         ┌───────────────▼─────────────────────────┐
         │ Runs each script & captures exit status │
         └───────────────┬─────────────────────────┘
                         │
       ┌─────────────────▼──────────────────┐
       │ On success: update log & rotate    │
       └─────────────────┬──────────────────┘
                         │
       ┌─────────────────▼──────────────────┐
       │ On failure: send `msmtp` alert     │
       └────────────────────────────────────┘
```

---

## ✅ Usage

In your playbook:

```yaml
- hosts: raspberrypi
  roles:
    - cron
```

1. Override `admin_email` (required) and optionally `zfs_pool_name`.  
2. Run `ansible-playbook -i inventory site.yml`.  
3. Verify scripts live under `/usr/local/bin/cron` and cron jobs in your crontab.  

---

> _Designed for quiet success, loud failure — ideal for a home-lab or small server environment._
