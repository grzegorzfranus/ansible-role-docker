# Ansible Role: Docker

|Source|Version|CI|License|
|------|-------|-------|-------|
|[![Source Code](https://img.shields.io/badge/source-github-blue.svg)](https://github.com/grzegorzfranus/ansible-role-docker)|[![Version](https://img.shields.io/github/v/release/grzegorzfranus/ansible-role-docker)](https://github.com/grzegorzfranus/ansible-role-docker/releases)|[![tests](https://github.com/grzegorzfranus/ansible-role-docker/actions/workflows/ci.yml/badge.svg)](https://github.com/grzegorzfranus/ansible-role-docker/actions)|[![Repository License](https://img.shields.io/badge/license-apache2.0-brightgreen.svg)](LICENSE)|

This Ansible role installs and configures Docker Engine from official repositories on Debian/Ubuntu and Enterprise Linux (Rocky). It provides secure defaults, optional logging to files, scheduled pruning and backups, and convenient features like user/group management and user-defined bridge networks.

## Main Actions

- Configure official Docker repositories with GPG verification
- Install Docker Engine packages (docker-ce, docker-ce-cli, containerd.io)
- Install optional plugins (docker-buildx-plugin, docker-compose-plugin)
- Configure Docker daemon (`/etc/docker/daemon.json`) with secure defaults
- Enforce docker socket permissions (group `docker`, mode `0660`)
- Manage `docker` group membership for selected users
- Configure rsyslog and logrotate for dockerd logs (optional)
- Configure systemd timer for `docker system prune` (optional)
- Configure systemd timer for configuration and metadata backups (optional)
- Configure auditd rule for `/var/run/docker.sock` (optional)
- Create user-defined Docker bridge networks (optional)

## Requirements

### Supported operating systems
List of officially supported operating systems:
| OS Family | Version | Status |
|-----------|---------|--------|
| Ubuntu    | 24.04 (Noble) | ✓ |
| Ubuntu    | 22.04 (Jammy) | ✓ |
| Debian    | 12 (Bookworm) | ✓ |
| Debian    | 11 (Bullseye) | ✓ |
| Rocky Linux | 9 | ✓ |

### Ansible version

Ansible >= 2.15

### Python version

Python >= 3.9

### Root access
This role requires root access, so either configure it in your inventory files, run it in a playbook with a global `become: true` or invoke the role in your playbook like:
```yaml
- hosts: servers
  become: true
  roles:
    - role: grzegorzfranus.docker
```

## Role Variables

### 1. General Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_role_action` | Define which parts of the role to execute (Options: `all`, `requirements`, `install`, `configure`, `logging`, `prune`, `backup`, `upgrade`) | `"all"` |
| `docker_service_enabled` | Enable/disable Docker service on boot | `true` |
| `docker_package_state` | Package state for Docker packages (`present` or `latest`) | `"present"` |
| `docker_users` | List of users to add to `docker` group | `[]` |

### 2. Packages and Plugins

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_install_compose_plugin` | Install `docker-compose-plugin` | `true` |
| `docker_install_buildx_plugin` | Install `docker-buildx-plugin` | `true` |
| `docker_extra_packages` | Additional package list to install | `[]` |

### 3. Daemon Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_enable_tcp_socket` | Enable dockerd TCP socket in addition to unix socket (no TLS) | `false` |
| `docker_tcp_listen` | TCP listen address when enabled | `"0.0.0.0:2375"` |
| `docker_log_driver` | Default log driver | `"json-file"` |
| `docker_log_opts.max-size` | Max log size per file | `"10m"` |
| `docker_log_opts.max-file` | Number of rotated files | `"3"` |
| `docker_registry_mirrors` | List of registry mirrors | `[]` |
| `docker_daemon_extra` | Extra key/values merged into `daemon.json` | `{}` |

### 4. Proxies and Registry

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_http_proxy` | HTTP proxy for docker service | `""` |
| `docker_https_proxy` | HTTPS proxy for docker service | `""` |
| `docker_no_proxy` | NO_PROXY for docker service | `""` |

### 5. Logging (rsyslog + logrotate)

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_configure_rsyslog` | Configure rsyslog to write dockerd logs to file | `false` |
| `docker_log_directory_path` | Log directory path (if empty, use `/var/log/docker.log`) | `"/var/log/docker"` |
| `docker_rsyslog_conf_name` | Name of rsyslog config file in `/etc/rsyslog.d/` | `"99-docker.conf"` |
| `docker_rsyslog_imjournal_filecreatemode` | imjournal file creation mode (workaround warning 2186) | `"0644"` |
| `docker_rsyslog_log_file_owner` | Owner of the docker log file (per-OS overrides) | `"root"` |
| `docker_rsyslog_log_file_group` | Group of the docker log file (per-OS overrides) | `"adm"` |
| `docker_rsyslog_log_file_mode` | Mode of the docker log file (per-OS overrides) | `"0640"` |
| `docker_logrotate_options.frequency` | Logrotate frequency (`hourly`, `daily`, `weekly`, `monthly`) | `"weekly"` |
| `docker_logrotate_options.count` | Number of rotated files to keep | `12` |
| `docker_logrotate_options.missingok` | Don’t error if log is missing | `true` |
| `docker_logrotate_options.compress` | Compress rotated logs | `true` |
| `docker_logrotate_options.nocreate` | Don’t create a new empty log | `false` |
| `docker_logrotate_options.copytruncate` | Use copytruncate to avoid reopen signaling | `true` |
| `docker_logrotate_options.dateext` | Add date extension to rotated logs | `true` |
| `docker_logrotate_options.archive_directory_path` | Archive directory for rotated logs | `"/var/log/docker"` |
| `docker_logrotate_create.enabled` | When not using copytruncate, create a fresh file | `true` |
| `docker_logrotate_create.mode` | New file mode (when creating) | `"0640"` |
| `docker_logrotate_create.owner` | New file owner (when creating) | `"root"` (per-OS overrides) |
| `docker_logrotate_create.group` | New file group (when creating) | `"adm"` (per-OS overrides) |

Note: On Ubuntu, the role sets log file owner/group to `syslog:adm`; on RHEL/Rocky it uses `root:root` with stricter mode. See OS-specific vars in `vars/`.

### 6. Prune Timer

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_prune_enabled` | Enable systemd prune timer | `false` |
| `docker_prune_frequency` | Prune cadence (`daily`, `weekly`, `monthly`) | `"weekly"` |
| `docker_prune_flags` | Prune flags | `"--all --volumes --force"` |

### 7. Backup Timer

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_backup_enabled` | Enable systemd backup timer | `false` |
| `docker_backup_path` | Backup destination directory | `"/var/backups/docker"` |
| `docker_backup_retention` | Number of retained backups | `7` |
| `docker_backup_include_volumes_metadata` | Include volumes list and metadata | `false` |

### 8. Rootless Mode

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_rootless_enabled` | Enable rootless Docker (opt-in) | `false` |
| `docker_rootless_users` | Users to configure for rootless | `[]` |

### 9. Networks

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_networks` | List of user-defined networks to create (e.g. `{ name, driver, subnet }`) | `[]` |

### 10. Audit

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_audit_socket` | Add auditd rule for `/var/run/docker.sock` if auditd present | `false` |

## 📁 Project Directory Structure

```
ansible-role-docker/
├── .github/                  # GitHub Actions workflows
│   └── workflows/           # CI/CD automation
│       ├── ci.yml           # CI pipeline (reusable ansible-ci.yml)
│       └── release.yml      # Release Please + Galaxy publish
├── .release-please-manifest.json # Release Please version manifest
├── release-please-config.json # Release Please configuration
├── defaults/
│   └── main.yml              # Default variables
├── handlers/
│   └── main.yml              # Service handlers
├── meta/
│   └── main.yml              # Role metadata
├── molecule/
│   └── default/
│       ├── molecule.yml      # Test configuration
│       ├── converge.yml      # Role execution playbook
│       ├── prepare.yml       # Test preparation tasks
│       └── verify.yml        # Verification tests
├── tasks/
│   ├── main.yml              # Main orchestration and flow control
│   ├── assert.yml            # Variable validation
│   ├── audit.yml             # Audit configuration
│   ├── backup.yml            # Backup configuration
│   ├── configure.yml         # Main configuration management
│   ├── install.yml           # Package installation
│   ├── logging.yml           # Logging configuration
│   ├── logrotate.yml         # Logrotate configuration
│   ├── network.yml           # Network configuration
│   ├── prerequisites.yml     # Prerequisites check
│   ├── prune.yml             # Prune configuration
│   ├── repository.yml        # Repository setup
│   └── service.yml           # Service control
├── templates/
│   ├── docker/               # Docker templates
│   ├── logrotate/            # Logrotate templates
│   ├── rsyslog/              # Rsyslog templates
│   ├── scripts/              # Helper scripts templates
│   └── systemd/              # Systemd service/timer templates
└── vars/
    ├── main.yml              # Common variables/constants
    ├── debian.yml            # Debian-specific variables
    └── redhat.yml            # RedHat-specific variables
```

## 🏷️ Tags

- `always` - Tasks that always run (variable loading and validation)
- `setup` - Setup tasks including OS-specific variables, requirements, and installation
- `init` - Initial setup tasks
- `validate` - Variable validation tasks
- `check` - Validation and verification tasks
- `requirements` - System requirements verification
- `install` - Package installation tasks
- `configure` - Service and daemon configuration tasks
- `logging` - rsyslog and logrotate configuration
- `logrotate` - Log rotation configuration tasks
- `prune` - Prune timer tasks
- `backup` - Backup timer tasks

## Example Playbooks

### Basic Docker Setup
```yaml
---
- name: Install Docker with secure defaults
  hosts: all
  become: true
  roles:
    - role: grzegorzfranus.docker
      vars:
        docker_users: ["alice", "bob"]
        docker_configure_rsyslog: true
```

### Advanced Configuration with Proxies, Mirrors, and Timers
```yaml
---
- name: Install Docker with proxies, mirrors, prune and backup timers
  hosts: docker_hosts
  become: true
  roles:
    - role: grzegorzfranus.docker
      vars:
        docker_users: ["deploy"]
        docker_registry_mirrors:
          - "https://mirror.gcr.io"
        docker_http_proxy: "http://proxy.local:3128"
        docker_https_proxy: "http://proxy.local:3128"
        docker_no_proxy: ".svc,.cluster.local,127.0.0.1,localhost"
        docker_prune_enabled: true
        docker_prune_frequency: weekly
        docker_backup_enabled: true
        docker_backup_retention: 14
```

## CI/CD Pipeline

### CI Pipeline

Runs on every Pull Request via centralized reusable workflow:

1. **Branch Name Lint** — enforces naming conventions (`feature/`, `bugfix/`, etc.)
2. **YAML Lint** — validates all YAML files
3. **Ansible Lint** — enforces best practices and guidelines compliance
4. **Security Scan** — TruffleHog secret detection
5. **Molecule Tests** — matrix across Debian 11/12, Ubuntu 22.04/24.04, and Rocky Linux 9
6. **Merge Check** — aggregated status check for branch protection

### Release & Publish

Automated via [Release Please](https://github.com/googleapis/release-please):

1. Merge to `main` → Release Please creates a Release PR with changelog
2. Merge Release PR → creates Git tag + GitHub Release
3. Galaxy publish triggers automatically on release using centralized action

## Contributing

Contributions, bug reports, and feature requests are welcome!

- Fork the repository and create your branch from `main`
- Use [Conventional Commits](https://www.conventionalcommits.org/) for commit messages:
  - `feat:` — new features (minor version bump)
  - `fix:` — bug fixes (patch version bump)
  - `docs:` — documentation changes
  - `refactor:` — code refactoring
  - `test:` — test additions
  - `ci:` — CI/CD changes
  - `chore:` — maintenance tasks
- Use branch naming convention: `feature/`, `bugfix/`, `hotfix/`, `docs/`, `refactor/`, `test/`, `chore/`, `ci/`
- Ensure your code passes all CI checks (YAML lint, Ansible lint, Molecule tests)
- Submit a pull request describing your changes

## 📝 License

This project is licensed under the Apache-2.0 License - see the LICENSE file for details.

## 👥 Author Information

This role was created by [Grzegorz Franus](https://github.com/grzegorzfranus).