# Ansible Role: ohmyzsh

This role installs and configures [Oh My Zsh](https://ohmyz.sh/) for a given user, using the excellent [`gantsign.oh-my-zsh`](https://github.com/gantsign/ansible-role-oh-my-zsh) role as a base. It adds custom themes, plugins, and user-defined configurations tailored for development environments.

## Features

- Installs Oh My Zsh for the target user
- Adds popular plugins:
  - `git`
  - `zsh-syntax-highlighting`
  - `zsh-autosuggestions`
  - `fzf`
- Applies a configurable theme (default: `jtriley`)
- Adds custom `.zsh` configuration snippets (e.g. aliases, editor settings, neofetch, etc.)

## Role Dependencies

- [`gantsign.oh-my-zsh`](https://galaxy.ansible.com/gantsign/oh-my-zsh) is automatically included via `meta/main.yml`.

## Variables

Defined in `defaults/main.yml`:

```yaml
ohmyzsh_user: "{{ ansible_user_id }}"

ohmyzsh_theme: "jtriley"

ohmyzsh_plugins:
  - git
  - zsh-syntax-highlighting
  - zsh-autosuggestions
  - fzf

ohmyzsh_update_mode: auto
ohmyzsh_update_frequency: 13

users:
  - username: "{{ ohmyzsh_user }}"
    oh_my_zsh:
      theme: "{{ ohmyzsh_theme }}"
      plugins: "{{ ohmyzsh_plugins }}"
      update_mode: "{{ ohmyzsh_update_mode }}"
      update_frequency: "{{ ohmyzsh_update_frequency }}"

ohmyzsh_custom_configs:
  - { name: "correction.zsh", content: 'ENABLE_CORRECTION="true"' }
  - { name: "editor.zsh", content: "export EDITOR='nano'" }
  - { name: "aliases.zsh", content: "alias l='lsd -A'\nalias rangerz='ranger --choosedir=$HOME/.rangerdir; LASTDIR=$(cat $HOME/.rangerdir); z \"$LASTDIR\"'" }
  - { name: "neofetch.zsh", content: "if [[ -o interactive ]]; then\n    neofetch\nfi" }
  - { name: "zoxide.zsh", content: 'eval \"$(zoxide init zsh)\"' }
  - { name: "lazydocker.zsh", content: 'export PATH=\"$HOME/.local/bin:$PATH\"' }
```

> You can override these variables to customize the oh-my-zsh installation.

## Example Playbook

```yaml
- name: Setup development shell
  hosts: testpi
  roles:
    - ohmyzsh
```

## License

MIT

## Author

Vantasin
