# Tailscale Role

This role installs, enables, and authenticates Tailscale on Debian-based systems.

## Variables

- `auth_key`: Your Tailscale Auth Key stored in ansible-vault (/inventories/production/group_vars/test_pis/main.yml).

## Example Usage

```yaml
- hosts: testpi
  roles:
    - tailscale
