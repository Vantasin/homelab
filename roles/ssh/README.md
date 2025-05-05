# Role: grant_ssh_access_to_live

Provision key-based SSH access from a **backup** host into a **live** host, and enforce key-only logins across your fleet.

---

## Role Overview

This role performs four idempotent steps:

1. **Generate** a 4096-bit RSA keypair on your backup server (if missing).  
2. **Fetch** the backup server’s public key.  
3. **Authorize** that key in the live server’s `~/.ssh/authorized_keys`.  
4. **Disable** SSH password (and challenge-response) authentication on all hosts. 

---

## Requirements

- **Ansible ≥ 2.9**  
- Your inventory should define two variables (e.g. in `group_vars/all.yml` or `group_vars/production.yml`):

  ```yaml
  backup_host: "backup"   # Inventory hostname for the backup server
  live_host:   "testpi"   # Inventory hostname for the live (target) server
  ```

  Those values override the defaults in the role’s `defaults/main.yml`. 

- No other user-specific variables are needed—the role leverages:
  - `ansible_user_id` to identify the SSH account  
  - `ansible_user_dir` to locate each host’s home directory  

---

## Variables

| Name               | Default                                              | Description                                   |
|--------------------|------------------------------------------------------|-----------------------------------------------|
| `backup_host`      | `{{ backup_host \| default('backup') }}`             | Inventory name of the backup server           |
| `live_host`        | `{{ live_host \| default('testpi') }}`               | Inventory name of the live (target) server    |

All file paths and ownership are derived from the built-in facts (`ansible_user_id`, `ansible_user_dir`). 

---

## Tasks

- **generate_ssh_keypair.yml**  
  Creates `id_rsa` and `id_rsa.pub` in `~/.ssh` on **backup_host** if they don’t exist. 

- **fetch_backup_pubkey.yml**  
  Slurps the public key from **backup_host** and stores it in a local fact. 

- **authorize_pubkey.yml**  
  Ensures `~/.ssh` exists on **live_host** and adds the backup key to `authorized_keys`. 

- **disable_password.yml**  
  Updates `/etc/ssh/sshd_config` on **all hosts** to disable `PasswordAuthentication` and `ChallengeResponseAuthentication`, then reloads `sshd`. 

---

## Usage

1. **Define** your hosts in `inventories/hosts.yml`:

   ```yaml
   all:
     children:
       production:
         hosts:
           backup:
           testpi:
   ```

2. **Set** the two variables in `group_vars/production.yml` (or `group_vars/all.yml`):

   ```yaml
   backup_host: "backup"
   live_host:   "testpi"
   ```

3. **Run** the role against your `production` group:

   ```yaml
   - hosts: production
     roles:
       - ssh
   ```

   - On **`testpi`** (`inventory_hostname == live_host`), the role will generate/fetch/authorize the key.  
   - On **every host**, it will disable SSH password logins.  

---

## Overrides & Customization

- To use a different SSH user on any host, set `ansible_user` in that host’s `host_vars/<hostname>.yml`. The role’s use of `ansible_user_id` and `ansible_user_dir` will automatically pick up your override.  
- You can also override `backup_host` and `live_host` at playbook or inventory level—Ansible variable precedence ensures the correct values are used.

---

With this configuration, you maintain a strict, least-privilege SSH policy: only your backup host can initiate connections to your live host, and all hosts reject password-based logins.
