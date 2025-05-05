## `roles/time_machine/README.md`

# 🕓 Ansible Role: `time_machine`

[![Ansible Role](https://img.shields.io/ansible/role/999999.svg?style=flat-square&color=blue)](https://galaxy.ansible.com/)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg?style=flat-square)](LICENSE)
[![Platform](https://img.shields.io/badge/platform-Linux%20%7C%20macOS-lightgrey.svg?style=flat-square)](https://github.com)
[![ZFS Support](https://img.shields.io/badge/ZFS-supported-brightgreen.svg?style=flat-square)]()
[![macOS Time Machine](https://img.shields.io/badge/Time%20Machine-compatible-blueviolet.svg?style=flat-square)]()

> Fully featured Samba + Avahi role with ZFS per-user Time Machine shares for macOS backups.

---

## 📚 Table of Contents

- [🚀 Features](#-features)
- [📁 Directory Structure](#-directory-structure)
- [🔧 Variables](#-variables)
- [🔁 Example `time_machine_users`](#-example-time_machine_users-auto-populated)
- [📝 Example Playbook](#-example-playbook)
- [🧪 Validations](#-validations)
- [📡 Share & Avahi Details](#-share--avahi-details)
- [⚙️ Optional Enhancements](#️-optional-enhancements)
- [🔐 Security Notes](#-security-notes)
- [📄 License](#-license)

---

## 🚀 Features

- ✅ Per-user Samba shares (`fruit:time machine = yes`)
- ✅ Bonjour/Avahi auto-discovery (like a real Time Capsule)
- ✅ ZFS datasets per user with optional tuning (e.g. compression, quota)
- ✅ System + Samba user creation from Ansible Vault
- ✅ Full config validation with `testparm` and `avahi-validate-service`
- ✅ Modular, scalable, and idempotent

---

## 📁 Directory Structure

This role uses a modular layout:

```
roles/time_machine/
├── defaults/
├── handlers/
├── meta/
├── tasks/
├── templates/
└── README.md
```

---

## 🔧 Variables

Defined in `defaults/main.yml`:

| Variable              | Default                                      | Description                               |
|-----------------------|----------------------------------------------|-------------------------------------------|
| `SMB_USER`            | `timemachine`                                | Samba username (from vault or inventory)  |
| `SMB_PASSWORD`        | `changeme`                                   | Samba password (from vault or inventory)  |
| `timemachine_dir`     | `{{ zfs_mount_point }}/TimeMachine`          | Root directory for backups                |
| `samba_max_size`      | `1000G`                                      | Quota per Time Machine share              |
| `time_machine_users`  | List of dicts with `username` and `password` | Automatically filled from `SMB_USER`      |

---

## 🔁 Example `time_machine_users` (auto-populated)

```yaml
time_machine_users:
  - username: "{{ SMB_USER }}"
    password: "{{ SMB_PASSWORD }}"
```

Or manually for multiple users:

```yaml
time_machine_users:
  - username: daniel
    password: "{{ vault_daniel_password }}"
  - username: alex
    password: "{{ vault_alex_password }}"
```

---

## 📝 Example Playbook

```yaml
- hosts: testpi
  roles:
    - time_machine
```

And in `vault.yml`:

```yaml
SMB_USER: daniel
SMB_PASSWORD: super-secret-password
zfs_pool_name: tank
zfs_mount_point: /tank
```

---

## 🧪 Validations

This role automatically:
- ✅ Validates Samba config using `testparm`
- ✅ Validates each Avahi `.service` file using `avahi-validate-service`

---

## 📡 Share & Avahi Details

Each user gets:
- A Samba share: `TimeMachine_daniel`, `TimeMachine_alex`, etc.
- An Avahi-broadcasted `.service` file at `/etc/avahi/services/`

Disks will appear automatically in macOS **Time Machine preferences**.

---

## ⚙️ Optional Enhancements

You can extend the role to:
- Add ZFS quotas: `-o quota=500G`
- Enable compression: `-o compression=zstd`
- Automate snapshots or replication

---

## 🔐 Security Notes

- Uses system users with `/usr/sbin/nologin`
- Passwords can (and should) be stored in **Ansible Vault**
- Role uses scoped `become: true` only where required

---

## 📄 License

MIT — © [Vantasin](https://github.com/Vantasin)
