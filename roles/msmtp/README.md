# Ansible Role: msmtp

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Ansible Role](https://img.shields.io/badge/Ansible-Role-blue?logo=ansible)](https://docs.ansible.com/)

This role installs and configures **msmtp** on Debian-based systems (e.g., Raspberry Pi OS), and makes it available as `/usr/sbin/sendmail` for tools like Fail2Ban, cron, etc. It also sets up secure logging, log rotation, and verifies mail delivery.

---

## Features

- **Install** `msmtp` and `logrotate`  
- **Deploy** `/etc/msmtprc` from a Jinja2 template with correct owner/group and `0640` permissions  
- **Symlink** `/usr/bin/msmtp` → `/usr/sbin/sendmail` so any `sendmail`-based MTA calls (Fail2Ban, cron, mailx, etc.) use `msmtp`  
- **Create** and secure the msmtp logfile at `/var/log/msmtp/msmtp.log`  
- **Configure** log rotation for the msmtp logfile via `/etc/logrotate.d/msmtp`  
- **Run** a test email command (`msmtp --debug`) to confirm settings  

---

## Variables

All variables live in `roles/msmtp/defaults/main.yml`. Override them via your inventory or vault:

```yaml
# Where to write the msmtp config
msmtp_config_path: "/etc/msmtprc"

# Where to log msmtp activity
msmtp_logfile_path: "/var/log/msmtp/msmtp.log"

# Path for the logrotate config
logrotate_config_path: "/etc/logrotate.d/msmtp"

# Template names (you normally don’t need to change these)
msmtp_conf_template: "msmtp.conf.j2"
logrotate_conf_template: "msmtp.logrotate.j2"

# SMTP connection settings (should come from vault or group_vars)
smtp_host:        smtp.example.com
smtp_port:        587
msmtp_from_email: no-reply@example.com
msmtp_user_vault: your_smtp_username
email_password:   your_smtp_password

# Recipient for the automated test email
test_email:       recipient@example.com
```

> **Tip:** Store sensitive values (`msmtp_user_vault`, `email_password`) in **Ansible Vault** under your `group_vars` or `host_vars`.

---

## Usage Example

```yaml
- hosts: testpi
  roles:
    - msmtp
```

When you run this playbook, it will:

1. **Install** `msmtp` & `logrotate`  
2. **Template** `/etc/msmtprc` with your SMTP creds  
3. **Symlink** `msmtp` to `/usr/sbin/sendmail`  
4. **Create** `/var/log/msmtp/msmtp.log` with secure perms  
5. **Deploy** a logrotate config  
6. **Send** a debug-level test email to `test_email`  

---

## Dependencies

- None

---

## License

MIT

---

## Author

Vantasin  
