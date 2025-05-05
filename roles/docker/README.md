# Ansible Role: Docker

This Ansible role installs and configures **Docker CE** and the **Docker Compose plugin** on a target system, using the trusted [`geerlingguy.docker`](https://galaxy.ansible.com/geerlingguy/docker) role as a dependency.

## Role Name

`docker`

## Requirements

- Ansible 2.9+
- Sudo privileges on the target host
- Debian-based OS (tested on Raspberry Pi OS and similar distros)

## Role Structure

```
roles/docker/
├── defaults/
│   └── main.yml
├── files/
├── handlers/
├── meta/
│   └── main.yml
├── tasks/
│   └── main.yml
└── templates/
```

## Dependencies

This role depends on:

- [`geerlingguy.docker`](https://galaxy.ansible.com/geerlingguy/docker)

It wraps this role with additional configuration and defaults.

## Role Variables

You can override any of these variables in your playbook or inventory:

```yaml
docker_edition: 'ce'
docker_packages:
  - "docker-{{ docker_edition }}"
  - "docker-{{ docker_edition }}-cli"
  - "docker-{{ docker_edition }}-rootless-extras"
docker_packages_state: present

docker_install_compose_plugin: true
docker_compose_package: docker-compose-plugin
docker_compose_package_state: present

docker_service_manage: true
docker_service_state: started
docker_service_enabled: true

docker_users:
  - "{{ ansible_user_id }}"
```

## Example Playbook

```yaml
- name: Install Docker and Compose
  hosts: testpi
  roles:
    - docker
```

## Author

[Vantasin](https://github.com/Vantasin)

## License

MIT
