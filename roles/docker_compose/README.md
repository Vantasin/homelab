# 🚀 Ansible Role: `docker_compose`

[![Ansible Role](https://img.shields.io/badge/Ansible-Role-blue?logo=ansible)](https://docs.ansible.com/)
[![License: MIT](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Status: Production Ready](https://img.shields.io/badge/status-Production%20Ready-success)]()
[![Platform: Raspberry Pi 5](https://img.shields.io/badge/platform-Raspberry%20Pi-lightgrey)]()

---

## 📚 Table of Contents

- [Features](#-features)
- [Directory Structure](#️-directory-structure)
- [How It Works](#-how-it-works)
- [Registry Integration](#-docker-registry-support)
- [Configuration](#-configuration)
- [Example Playbook](#-example-playbook)
- [Dependencies](#-dependencies)
- [Debugging](#-debugging)
- [License](#-license)
- [Maintainer](#-maintainer)

---

## 📦 Features

- ✅ Auto-discovers stack names from `templates/` subdirectories
- 📁 Creates matching persistent directories under `{{ docker_dir }}`
- 🧩 Renders all `.j2` templates per stack:
  - `docker-compose.yml.j2 → docker-compose.yml`
  - `env.j2 → .env`
  - `*.sh.j2 → *.sh` (executable)
  - `prometheus.yml.j2 → prometheus.yml`
- 🔁 Only restarts changed stacks using `community.docker.docker_compose_v2`
- 🔐 Auto-generates `htpasswd` for Docker Registry stack
- 🧪 Verbose logging enabled via `-v`
- 💡 Gracefully skips if no templates are found

---

## 🗂️ Directory Structure

```bash
roles/docker_compose/
├── defaults/
├── handlers/
├── meta/
├── tasks/
│   ├── main.yml
│   ├── prepare.yml
│   ├── render_templates.yml
│   ├── render_templates_per_stack.yml
│   ├── render_debug.yml
│   ├── deploy.yml
│   └── registry_htpasswd.yml
├── templates/
│   ├── Immich/
│   ├── Prometheus_node-exporter_Grafana/
│   ├── Registry/
│   └── ...
└── README.md
```

---

## 🧠 How It Works

### 🔍 Stack Discovery

Each folder in `templates/` becomes a deployable stack:

```yaml
docker_stack:
  - name: Immich
  - name: Prometheus_node-exporter_Grafana
  - name: Registry
```

Stacks without a `docker-compose.yml.j2` are skipped automatically.

---

### 🧩 Template Rendering

A single task renders all `*.j2` files within each stack subdirectory:

| Template                  | Output File                 | Permissions |
|---------------------------|-----------------------------|-------------|
| `docker-compose.yml.j2`   | `docker-compose.yml`        | `0644`      |
| `env.j2`                  | `.env`                      | `0600`      |
| `*.sh.j2`                 | `*.sh`                      | `0755`      |
| `prometheus.yml.j2`       | `prometheus.yml`            | `0644`      |
| `*.conf.j2`, `*.json.j2`  | *respective config*         | `0644`      |

All rendered files are saved under `{{ docker_dir }}/<stack>/`.

---

## 🔐 Docker Registry Support

If a `Registry` stack is detected:

- Checks for the presence of `htpasswd`
- If missing, renders and executes `generate_htpasswd.sh`
- Username/password are provided via `REGISTRY_USER` / `REGISTRY_PASSWORD`
- Automatically installs `apache2-utils` if missing

---

## 🔧 Configuration

### Example: `group_vars/all.yml`

```yaml
zfs_mount_point: "/tank"
docker_dir: "{{ zfs_mount_point }}/docker"

REGISTRY_USER: admin
REGISTRY_PASSWORD: supersecure

grafana_admin_user: admin
grafana_admin_password: supersecure
prometheus_port: "9090"
node_exporter_port: "9100"
grafana_port: "3000"

ansible_python_interpreter: /usr/bin/python3
timezone: "America/Toronto"
```

---

## 📜 Example Playbook

```yaml
- name: Deploy Docker Compose stacks
  hosts: testpi
  roles:
    - docker_compose
```

---

## 📦 Dependencies

This role depends on the following roles and collections:

### Required Roles

| Role       | Purpose                                        |
|------------|------------------------------------------------|
| `zfs`      | Prepares and mounts the ZFS pool where Docker data lives (`{{ docker_dir }}`) |
| `docker`   | Installs Docker Engine and Docker Compose V2   |

### Required Collections

```yaml
collections:
  - community.docker
  - community.general
```

Install via:

```bash
ansible-galaxy install -r requirements.yml
```

---

## 🧪 Debugging

Run with verbosity for diagnostics:

```bash
ansible-playbook playbook.yml -v
```

This enables logs like:

- Discovered stack templates
- Rendered output file paths
- Changed stacks being restarted
- Script detection output

---

## 📎 License

MIT — use it freely.

---

## 👨‍🔧 Maintainer

Developed and maintained by [@Vantasin](https://github.com/Vantasin)
