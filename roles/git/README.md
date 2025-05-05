# Ansible Role: git

[![Ansible Role](https://img.shields.io/badge/role-vantasin.git-blue.svg?logo=ansible)](https://github.com/Vantasin/raspberry-pi)
[![License: MIT](https://img.shields.io/badge/license-MIT-green.svg)](https://opensource.org/licenses/MIT)
[![Build Status](https://img.shields.io/badge/build-passing-brightgreen.svg)](#)
[![Platform](https://img.shields.io/badge/platform-Debian%2FUbuntu-lightgrey.svg?logo=linux)](#)

## Description

This role provisions a Git storage directory on a ZFS pool, ensuring the dataset is created (if missing) and mounted with correct permissions. It is ideal for home labs or production environments where Git repositories are stored directly on ZFS-backed storage.

---

## Role Variables

### Required

| Variable         | Description                          | Default                          |
|------------------|--------------------------------------|----------------------------------|
| `zfs_pool_name`  | Name of the ZFS pool                 | `""`                             |
| `zfs_mount_point`| Path where the ZFS pool is mounted   | _Must be set externally_         |

### Optional

| Variable         | Description                          | Default                          |
|------------------|--------------------------------------|----------------------------------|
| `git_dir`        | Full path for Git directory          | `{{ zfs_mount_point }}/Git`      |
| `git_user`       | Owner of the Git directory           | `{{ ansible_user_id }}`          |
| `git_group`      | Group for the Git directory          | `{{ ansible_user_gid | default(ansible_user_id) }}` |
| `git_mode`       | Permission mode for the directory    | `"0775"`                         |

---

## Tasks

- Checks for existence of the ZFS dataset.
- Creates the dataset if not present.
- Ensures the Git directory has correct ownership and permissions.

---

## Example Usage

```yaml
- hosts: testpi
  roles:
    - role: git
```

---

## Author

Developed and maintained by [Vantasin](https://github.com/Vantasin)

---

## License

MIT
