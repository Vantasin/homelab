# Nano Role

This role installs Nano syntax highlighting and configures `.nanorc` with preferred settings.

## Features

- Clones `scopatz/nanorc` for syntax highlighting
- Creates `.nanorc` if missing
- Configures Nano with:
  - line numbers
  - auto-indentation
  - tab size of 4
  - soft wrapping

## Usage

```yaml
- hosts: testpi
  roles:
    - nano
