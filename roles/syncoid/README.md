# 🔁 Ansible Role: `syncoid`

[![Ansible Role](https://img.shields.io/badge/Ansible-Role-blue?logo=ansible)](https://docs.ansible.com/)
[![Platform: Debian | ZFS](https://img.shields.io/badge/platform-Debian%20%7C%20ZFS-blue)](https://openzfs.org/)

## Description
This role configures a **pull-based** ZFS snapshot replication from a live server into a backup server using [syncoid]. It:

- Deploys a lightweight **wrapper** script that invokes `syncoid` with buffering and auto-reconnect.  
- Deploys a **notifier** script that emails you on failure via `msmtp`.  
- Schedules a **cron job** (daily at 03:30) to run the notifier, which in turn runs the replication. 

## Features
- **Host validation**: Ensures the role only runs on your designated backup host.  
- **Idempotent package install**: Installs `syncoid`, ZFS CLI tools, `mbuffer` and `autossh`.  
- **Modular tasks**: Separate tasks for validation, dependencies, deployment and scheduling.  
- **Configurable variables**: All non-inventory values live in `defaults/main.yml`.  
- **Email alerts**: Notifies on replication failure with log output.  

## Requirements
- **Ansible ≥ 2.9**  
- ZFS support on both source and destination (e.g. `zfsutils-linux`).  
- Passwordless SSH from **backup** → **live** (handled by your SSH role).  
- `msmtp` (or similar MTA) configured on the backup server. 

## Inventory Setup
Define these in `inventory/group_vars/all.yml` (or an appropriate group_vars file):

```yaml
backup_host: backup        # Inventory name for your backup server
live_host:   live          # Inventory name for your live (source) server
admin_email: ops@example.com
```

## Role Variables (`defaults/main.yml`)

| Variable                       | Default                                              | Description                                                                           |
|--------------------------------|------------------------------------------------------|---------------------------------------------------------------------------------------|
| `backup_host`                  | n/a                                                  | Inventory name of the backup server                                                   |
| `live_host`                    | n/a                                                  | Inventory name of the live (source) server                                            |
| `syncoid_cron_script_path`     | `/usr/local/bin`                                     | Directory to install wrapper & notifier scripts                                       |
| `admin_email`                  | `ops@example.com`                                    | Email address to send failure alerts                                                  |
| `syncoid_ssh_key_path`         | `{{ hostvars[backup_host].ansible_user_dir }}/.ssh/id_rsa` | Path to the SSH private key on the backup server (from your SSH role)           |
| `syncoid_source_host`          | `{{ hostvars[live_host].ansible_host }}`            | Hostname or IP of the live server                                                     |
| `syncoid_source_user`          | `{{ hostvars[live_host].ansible_user | default('zfs') }}` | SSH user to connect as on the live server                                                |
| `syncoid_source_dataset`       | `{{ hostvars[live_host].zfs_pool_name | default('tank') }}` | ZFS dataset (or pool) on the live server to pull from                                |
| `syncoid_dest_dataset`         | `{{ syncoid_source_dataset }}`                       | Local ZFS dataset to receive snapshots                                                |
| `syncoid_packages`             | `['zfsutils-linux','syncoid','mbuffer','autossh']`   | List of packages installed for replication                                            |
| `syncoid_mbuffer_size`         | `128M`                                               | Memory size for `mbuffer`                                                             | 

## Tasks
All task files are included from `tasks/main.yml`:

1. **validate_hosts.yml** – Asserts you’re on the correct host and that `live_host` exists.  
2. **install_deps.yml** – Installs required packages via the `package` module.  
3. **deploy_wrapper.yml** – Renders `syncoid_wrapper.sh.j2` into the script path.  
4. **deploy_notifier.yml** – Renders `syncoid_cron_notify.sh.j2` into the script path.  
5. **schedule_cron.yml** – Adds the cron job to run daily at 03:30. 

## Usage Example

```yaml
- hosts: "{{ backup_host }}"
  roles:
    - role: syncoid
```

Ensure you’ve already run your **SSH role** against both hosts so that the backup server can connect password-lessly to the live server.

[syncoid]: https://github.com/jimsalterjrs/syncoid  
