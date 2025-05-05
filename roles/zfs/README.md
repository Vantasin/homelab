# 📦 ZFS Ansible Role

This role installs and configures ZFS, detects usable drives, **safely imports existing pools**, and creates a new mirrored pool if one does not already exist. It applies optimized settings for Raspberry Pi 5 and other systems.

---

## 🔧 Features

- ✅ Installs ZFS across Debian, Arch, RedHat, and FreeBSD systems
- 🔍 Auto-detects unmounted, non-system disks for ZFS provisioning
- 🔄 **Idempotent import-before-create logic** for safe re-provisioning after OS reinstall
- 🧱 Creates ZFS pool with `ashift=12` (4K sector alignment for USB/SSD)
- 🧮 Configures compression (`lz4`), disables `atime`, and tunes ARC size
- 💾 Persists pool metadata in `/etc/ansible/facts.d/zfs_pool.fact`
- ⚙️ Modular, safe, and rerunnable

---

## 📂 Role Structure

```text
roles/zfs/
├── defaults/main.yml         # Variables
├── handlers/main.yml         # ZFS reload handler
├── meta/main.yml             # Metadata
└── tasks/
    ├── main.yml              # Task includes
    ├── install.yml           # ZFS installation
    ├── detect.yml            # Disk detection
    ├── import.yml            # Import existing pool
    ├── create.yml            # Create pool if needed
    ├── config.yml            # Compression, atime, arc tuning
```

---

## ⚙️ Variables

| Variable                     | Default                           | Description                                      |
|-----------------------------|-----------------------------------|--------------------------------------------------|
| `zfs_pool_name`             | `tank`                            | Name of the ZFS pool                             |
| `zfs_pool_type`             | `mirror`                          | Topology (`mirror`, `raidz`, etc.)               |
| `zfs_required_disk_count`   | `2`                               | Number of disks required                         |
| `excluded_disks`            | `[]`                              | Additional disks to exclude                      |
| `persist_file`              | `/etc/ansible/facts.d/zfs_pool.fact` | Pool metadata file                         |
| `zfs_arc_max`               | `2147483648`                      | Max ARC size in bytes (2GB default)              |
| `zfs_enable_compression`    | `true`                            | Enable pool-level compression                    |
| `zfs_compression_algorithm` | `lz4`                             | Compression algorithm                            |
| `zfs_disable_atime`         | `true`                            | Disable access time tracking                     |
| `zfs_dry_run`               | `false`                           | Simulate pool creation without applying changes  |

---

## 🔁 Workflow

1. **Install ZFS** on the target system.
2. **Detect usable disks**, excluding boot/mounted disks and user-defined exclusions.
3. **Try to import existing ZFS pool** by name (e.g., after OS reinstall).
4. **If not imported**, and candidate disk count matches, create new pool.
5. **Configure** pool settings: compression, atime, ARC, etc.
6. **Persist** pool metadata to Ansible fact file.

---

## 🚀 Example Playbook

```yaml
- name: Setup ZFS on Raspberry Pi
  hosts: testpi
  roles:
    - role: zfs
```

---

## 🛡️ Safety & Idempotency

- If the pool already exists but is not imported (e.g., fresh OS), it will be safely imported.
- The pool will **not** be created again if it already exists or was imported.
- Dry-run mode (`zfs_dry_run: true`) will show the commands without making changes.

---

## ✍️ Author

**Vantasin**
