# 🧪 Ansible Role: `sanoid`

[![Ansible Role](https://img.shields.io/badge/Ansible-Role-blue?logo=ansible)](https://docs.ansible.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)
[![Platform: Debian / ZFS](https://img.shields.io/badge/platform-Debian%20%7C%20ZFS-blue)](https://openzfs.org/)

---

## 📦 Features

- ✅ Installs **Sanoid** for automatic ZFS snapshot management
- ✅ Creates and deploys a custom `sanoid.conf`
- ✅ Schedules automatic snapshots via systemd by default (no action required)
- ✅ Works seamlessly on Debian-based systems with ZFS

---

## 📂 Role Structure

```
roles/sanoid/
├── defaults/
│   └── main.yml
├── meta/
│   └── main.yml
├── tasks/
│   └── main.yml
├── templates/
│   ├── sanoid.conf.j2
```

---

## ⚙️ Variables

| Variable                   | Default                  | Description                                                  |
|---------------------------|--------------------------|--------------------------------------------------------------|
| `pool`                    | `{{ zfs_pool_name }}`     | Name of the ZFS pool to manage via Sanoid                    |

Set these in your `group_vars`, `host_vars`, or inventory.

---

## 🛠️ Templates

### `sanoid.conf.j2`

Defines snapshot retention using a default template:

```ini
[{{ pool }}]
use_template = default
recursive = yes

[template_default]
autosnap = yes
autoprune = yes
hourly = 0
daily = 7
weekly = 4
monthly = 6
yearly = 0
```

---

## ✅ Usage

Add this role to your playbook:

```yaml
- hosts: testpi
  roles:
    - sanoid
```

Then run:

```bash
ansible-playbook playbook/site.yml
```

Ensure your `pool` variables is properly set.

---

## 🔒 Requirements

- Debian-based OS
- ZFS installed and configured

---

## 🧠 Tips

- To customize retention, override `sanoid.conf.j2` in your own role or playbook.
- Use with `syncoid` for replication to a remote ZFS server.
- Combine with a `zfs_scrub` cron role for full health checks.

---

> _Built for reliability. Quiet unless something goes wrong._
