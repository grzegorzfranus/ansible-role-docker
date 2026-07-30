# Ansible Role: Docker

|Source|Version|CI|License|
|------|-------|--|-------|
|[![Source Code](https://img.shields.io/badge/source-github-blue.svg)](https://github.com/grzegorzfranus/ansible-role-docker)|[![Version](https://img.shields.io/github/v/release/grzegorzfranus/ansible-role-docker)](https://github.com/grzegorzfranus/ansible-role-docker/releases)|[![CI](https://github.com/grzegorzfranus/ansible-role-docker/actions/workflows/ci.yml/badge.svg)](https://github.com/grzegorzfranus/ansible-role-docker/actions/workflows/ci.yml)|[![Repository License](https://img.shields.io/badge/license-apache2.0-brightgreen.svg)](LICENSE)|

This Ansible role installs and configures Docker Engine from official repositories on Debian/Ubuntu and Enterprise Linux systems. It provides secure defaults, daemon configuration, optional dedicated logging, scheduled pruning and backups, and comprehensive validation with professional-grade error reporting.

## ✨ Features

- 🐳 **Docker Engine**: Install from official Docker repositories with GPG verification
- 🔧 **Daemon Configuration**: Customizable `daemon.json` with secure defaults
- 🔌 **Plugin Support**: Optional Compose and Buildx plugin installation
- 📝 **Dedicated Logging**: Optional rsyslog integration with logrotate
- 🧹 **Automated Pruning**: Configurable systemd timer for `docker system prune`
- 💾 **Automated Backups**: Scheduled backup of Docker configuration and metadata
- 🔒 **Security**: Socket permissions, audit rules, proxy configuration
- 🌐 **Network Management**: Create user-defined Docker bridge networks
- 📊 **Comprehensive Validation**: System compatibility checks and connectivity tests
- 🧪 **Enhanced Error Reporting**: Detailed diagnostics for troubleshooting
- 🔄 **Lifecycle Management**: Clean install and uninstall via `docker_state`
- 🧪 **Container Testing**: Full Molecule test suite for CI/CD integration

## 📋 Requirements

### Supported operating systems

| OS Family | Version | Status |
|-----------|---------|--------|
| Ubuntu | 26.04 (Resolute) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Ubuntu | 24.04 (Noble) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Ubuntu | 22.04 (Jammy) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 13 (Trixie) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 12 (Bookworm) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 11 (Bullseye) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Rocky Linux | 9 | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| RHEL | 9 | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| AlmaLinux | 9 | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |

### Ansible version

Ansible >= 2.15

### Python version

Python >= 3.9

### Collections

- `community.docker` >= 3.0.0

### Setup module

The role uses facts gathered by Ansible on the remote host. If you disable the Setup module in your playbook, the role will not work properly.

### Root access

This role requires root access for package installation and service management. Each task explicitly defines `become: true` where needed — do not set `become: true` globally.

## 🚀 Quick Start

### 1. Basic Docker Installation

```yaml
---
- name: Install Docker with secure defaults
  hosts: all
  roles:
    - role: grzegorzfranus.docker
      vars:
        docker_users: ["alice", "bob"]
```

### 2. Advanced Configuration with Proxies and Timers

```yaml
---
- name: Install Docker with proxies, mirrors, prune and backup timers
  hosts: docker_hosts
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
        docker_configure_rsyslog: true
```

### 3. Run the playbook

```bash
ansible-playbook -i inventory docker-setup.yml
```

## ⚙️ Configuration

### Default Configuration

The role comes with production-ready defaults:

```yaml
# Lifecycle
docker_state: "present"

# Service management
docker_service_enabled: true
docker_package_state: "present"

# Validation and diagnostics
docker_validate_system: true
```

### Logging Configuration Example

Enable dedicated logging to separate Docker logs from system logs:

```yaml
---
- name: Docker with Dedicated Logging
  hosts: all
  roles:
    - role: grzegorzfranus.docker
      vars:
        docker_configure_rsyslog: true
        docker_log_directory_path: "/var/log/docker"
        docker_logrotate_options:
          frequency: "daily"
          count: 30
          missingok: true
          compress: true
          nocreate: false
          dateext: true
          archive_directory_path: "/var/log/docker"
          copytruncate: true
```

## 📊 Variables

### Service State (Lifecycle)

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_state` | Control overall role behavior: `present` to install, `absent` to uninstall | `"present"` |
| `docker_remove_data` | Opt-in flag when `docker_state: absent` to purge `/var/lib/docker` engine data | `false` |

### General Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_service_enabled` | Enable/disable Docker service on boot | `true` |
| `docker_manage_service_restart` | Allow role handlers to restart Docker/rsyslog services and run `augenrules --load` (set `false` in container test environments) | `true` |
| `docker_package_state` | Package state for Docker packages (`present` or `latest`) | `"present"` |
| `docker_users` | List of existing users to add to `docker` group (fails if user does not exist; missing accounts are not created) | `[]` |

### Packages and Plugins

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_install_compose_plugin` | Install `docker-compose-plugin` | `true` |
| `docker_install_buildx_plugin` | Install `docker-buildx-plugin` | `true` |
| `docker_extra_packages` | Additional package list to install | `[]` |

### Daemon Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_enable_tcp_socket` | Enable dockerd TCP socket (replaces ExecStart drop-in while preserving `--containerd=`) | `false` |
| `docker_tcp_listen` | TCP listen address when enabled | `"0.0.0.0:2375"` |
| `docker_tcp_tls_enabled` | Enable TLS authentication for TCP socket | `false` |
| `docker_tcp_tls_cacert` | Absolute path to CA certificate for TLS verification | `""` |
| `docker_tcp_tls_cert` | Absolute path to server certificate for TLS | `""` |
| `docker_tcp_tls_key` | Absolute path to server private key for TLS | `""` |
| `docker_tcp_insecure_acknowledged` | Opt-in flag required to bind non-loopback TCP socket without TLS | `false` |
| `docker_log_driver` | Default log driver | `"json-file"` |
| `docker_log_opts` | Log driver options (must include `max-size` and `max-file`) | `{"max-size": "10m", "max-file": "3"}` |
| `docker_registry_mirrors` | List of registry mirrors | `[]` |
| `docker_daemon_extra` | Extra key/values merged into `daemon.json` | `{}` |

### Proxy and Registry

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_http_proxy` | HTTP proxy for Docker service | `""` |
| `docker_https_proxy` | HTTPS proxy for Docker service | `""` |
| `docker_no_proxy` | NO_PROXY for Docker service | `""` |

### Logging (rsyslog + logrotate)

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_configure_rsyslog` | Configure rsyslog to write dockerd logs to file | `false` |
| `docker_log_directory_path` | Log directory path (multi-component absolute path; cannot resolve to `/`, `/var`, `/var/log`, `/etc`, or `/usr`) | `"/var/log/docker"` |
| `docker_rsyslog_conf_name` | Name of rsyslog config file in `/etc/rsyslog.d/` | `"99-docker.conf"` |
| `docker_rsyslog_log_file_owner` | Owner of the docker log file (empty string falls back to OS default) | `""` |
| `docker_rsyslog_log_file_group` | Group of the docker log file (empty string falls back to OS default) | `""` |
| `docker_rsyslog_log_file_mode` | Mode of the docker log file | `"0640"` |
| `docker_logrotate_options.frequency` | Logrotate frequency (`hourly`, `daily`, `weekly`, `monthly`) | `"weekly"` |
| `docker_logrotate_options.count` | Number of rotated files to keep | `12` |
| `docker_logrotate_options.missingok` | Don't error if log is missing | `true` |
| `docker_logrotate_options.compress` | Compress rotated logs | `true` |
| `docker_logrotate_options.nocreate` | Don't create a new empty log | `false` |
| `docker_logrotate_options.copytruncate` | Use copytruncate to avoid reopen signaling | `true` |
| `docker_logrotate_options.dateext` | Add date extension to rotated logs | `true` |
| `docker_logrotate_options.archive_directory_path` | Archive directory for rotated logs | `"/var/log/docker"` |
| `docker_logrotate_create.enabled` | When not using copytruncate, create a fresh file | `true` |
| `docker_logrotate_create.mode` | New file mode (when creating) | `"0640"` |
| `docker_logrotate_create.owner` | New file owner (when creating, resolves OS default or override) | `""` |
| `docker_logrotate_create.group` | New file group (when creating, resolves OS default or override) | `""` |

Note: If `docker_rsyslog_log_file_owner` or `docker_rsyslog_log_file_group` are left empty (the default),
the role falls back to OS distribution defaults: `syslog:adm` on Ubuntu, `root:adm` on Debian, and `root:root`
on EL9. Setting explicit non-empty values overrides the OS default for both live log files and logrotate
creation settings.

### Prune Timer

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_prune_enabled` | Enable systemd prune timer | `false` |
| `docker_prune_frequency` | Prune cadence (`daily`, `weekly`, `monthly`) | `"weekly"` |
| `docker_prune_flags` | Prune flags (dangling resources only by default) | `"--force"` |

> ⚠️ **Note:** To enable aggressive pruning of all unused images and volumes, set `docker_prune_flags: "--all --volumes --force"`. Use with caution as this permanently deletes unreferenced volumes and stopped containers.

### Backup Timer

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_backup_enabled` | Enable systemd backup timer | `false` |
| `docker_backup_frequency` | Backup cadence (`hourly`, `daily`, `weekly`, `monthly`) | `"daily"` |
| `docker_backup_path` | Backup destination directory | `"/var/backups/docker"` |
| `docker_backup_retention` | Number of retained backups | `7` |
| `docker_backup_include_volumes_metadata` | Include volumes list and metadata | `false` |

### Networks

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_networks` | List of user-defined networks to create (e.g. `{ name, driver, subnet }`) | `[]` |

### Audit

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_audit_socket` | Add auditd rule for `/run/docker.sock` if auditd present | `false` |

### System Validation Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_validate_system` | Enable comprehensive system validation checks | `true` |
| `docker_validate_connectivity` | Enable network connectivity verification to Docker repositories | `true` |
| `docker_validate_prerequisites` | Enable architecture and package manager validation | `true` |
| `docker_collect_system_info` | Collect detailed system information for diagnostics | `true` |
| `docker_connectivity_timeout` | Timeout for network connectivity checks (seconds) | `10` |

## 📌 Role Properties

| Property | Value | Description |
|----------|-------|-------------|
| **Idempotent** | ✅ Yes | Running the role multiple times with the same parameters produces the same result. |
| **Atomic** | ❌ No | The role can be partially applied. A failure mid-execution may leave the system in an intermediate state. Use `docker_state: absent` to clean up. |
| **Check Mode** | ✅ Supported | Most tasks work in check mode. Mutating commands are skipped. |
| **Diff Mode** | ✅ Supported | Template tasks support diff mode for change preview. |

## 📤 Role Output

This role does not set any public output facts. All internal facts use the `__docker_` double-underscore prefix and are not part of the public interface.

To inspect diagnostic data at runtime, run the playbook with `-v` or `-vv` verbosity flags — the role provides detailed debug output at `verbosity: 1`.

## 🔍 Verification

After deployment, verify Docker is working correctly:

```bash
# Check service status
sudo systemctl status docker

# Verify Docker version
docker version

# Run a test container
docker run --rm hello-world

# Check Docker info
docker info
```

## 🛡️ Security Features

- ✅ **GPG-verified Repositories**: Official Docker repositories with signed-by keyring
- ✅ **Socket Permissions**: Docker socket enforced to group `docker`, mode `0660`
- ✅ **Systemd Overrides**: ExecStart drop-in rendered only when `docker_enable_tcp_socket: true` to prevent flag conflicts (preserves `--containerd=`)
- ✅ **Audit Support**: Optional auditd rule for `/run/docker.sock` monitoring
- ✅ **Proxy Support**: Configurable HTTP/HTTPS proxy via systemd drop-ins
- ✅ **Minimal Attack Surface**: TCP socket disabled by default; non-loopback binds require TLS or explicit opt-in acknowledgement

### Uninstall

To remove Docker and its configuration from a host:

```yaml
---
- name: Uninstall Docker
  hosts: all
  roles:
    - role: grzegorzfranus.docker
      vars:
        docker_state: absent
```

## 🏷️ Tags

- `always` - Tasks that always run (variable loading and validation)
- `docker_setup` - Setup and configuration tasks
- `docker_init` - Initial environment setup and variable loading
- `docker_validate` - Variable validation and system checks
- `docker_requirements` - Prerequisite and repository configuration tasks
- `docker_install` - Package installation tasks
- `docker_configure` - Service and daemon configuration tasks
- `docker_service` - Service management tasks
- `docker_logging` - rsyslog and logrotate configuration
- `docker_prune` - Prune timer tasks
- `docker_backup` - Backup timer tasks
- `docker_audit` - Audit configuration tasks

## 🔧 Troubleshooting

### Service Issues

```bash
# Check if docker daemon is running
sudo systemctl status docker

# View detailed systemd logs for docker
sudo journalctl -u docker -f

# Restart docker service
sudo systemctl restart docker
```

### Permission Issues

If you cannot run `docker` commands without `sudo`, verify that your user is added to the `docker` group:

```bash
# Check group membership
groups

# Add current user to docker group (requires re-login)
sudo usermod -aG docker $USER
```

## 📁 File Structure

```
ansible-role-docker/
├── .github/
│   ├── ISSUE_TEMPLATE/                # Issue templates for bug, feature, task
│   │   ├── bug_report.yml
│   │   ├── config.yml
│   │   ├── feature_request.yml
│   │   └── task.yml
│   ├── PULL_REQUEST_TEMPLATE/         # Pull request description template
│   │   └── pull_request_template.md
│   ├── workflows/
│   │   ├── ci.yml                     # CI pipeline
│   │   └── release.yml                # Release Please + Galaxy publish
│   └── dependabot.yml                 # Dependabot configuration for GitHub Actions
├── .release-please-manifest.json # Release Please version manifest
├── release-please-config.json    # Release Please configuration
├── CHANGELOG.md              # Version history and changes
├── LICENSE                   # Apache-2.0 license
├── README.md                # This documentation file
├── defaults/
│   └── main.yml             # Default configuration variables
├── handlers/
│   └── main.yml             # Service restart and reload handlers
├── meta/
│   ├── main.yml             # Role metadata and Galaxy information
│   └── argument_specs.yml   # Ansible-native argument validation
├── molecule/                 # Molecule testing framework
│   ├── default/              # Default installation and verification scenario
│   │   ├── molecule.yml     # Test configuration
│   │   ├── converge.yml     # Role execution playbook
│   │   ├── prepare.yml      # Test preparation tasks
│   │   └── verify.yml       # Verification tests with strict assertions
│   ├── logging/              # Rsyslog and logrotate default configuration scenario
│   │   ├── molecule.yml     # Test configuration
│   │   ├── converge.yml     # Single-play convergence playbook (default logging)
│   │   ├── prepare.yml      # Test preparation tasks (logrotate package)
│   │   └── verify.yml       # Verification tests for rsyslog, logrotate, and OS default log owner
│   ├── logging-override/     # Rsyslog owner override and copytruncate: false scenario
│   │   ├── molecule.yml     # Test configuration
│   │   ├── converge.yml     # Single-play convergence playbook (owner override)
│   │   ├── prepare.yml      # Test preparation tasks (logrotate package)
│   │   └── verify.yml       # Verification tests for owner override and logrotate create directive consistency
│   └── uninstall/            # Complete role uninstall scenario
│       ├── molecule.yml     # Test configuration
│       ├── converge.yml     # Multi-play convergence playbook (install, uninstall, guard check)
│       ├── prepare.yml      # Test preparation tasks
│       └── verify.yml       # Verification tests asserting absence of packages/units and protection of /var/log
├── tasks/
│   ├── main.yml             # Main task orchestration
│   ├── assert.yml           # Variable validation
│   ├── validate.yml         # System validation and diagnostics
│   ├── prerequisites.yml    # System preparation
│   ├── repository.yml       # Repository configuration
│   ├── install.yml          # Package installation
│   ├── configure.yml        # Daemon and systemd configuration
│   ├── service.yml          # Service management
│   ├── logging.yml          # Logging and logrotate configuration
│   ├── network.yml          # Network configuration
│   ├── prune.yml            # Prune timer configuration
│   ├── backup.yml           # Backup timer configuration
│   ├── audit.yml            # Audit configuration
│   └── remove.yml           # Role removal tasks
├── templates/
│   ├── docker/              # Docker daemon templates
│   │   └── daemon.json.j2
│   ├── logrotate/           # Logrotate templates
│   │   └── docker.j2
│   ├── rsyslog/             # Rsyslog templates
│   │   └── docker.conf.j2
│   ├── scripts/             # Helper scripts
│   │   └── docker-backup.sh.j2
│   └── systemd/             # Systemd service/timer templates
│       ├── docker-backup.service.j2
│       ├── docker-backup.timer.j2
│       ├── docker-prune.service.j2
│       ├── docker-prune.timer.j2
│       ├── docker-service-override.conf.j2
│       ├── docker-service-proxy.conf.j2
│       └── docker-socket-override.conf.j2
└── vars/
    ├── main.yml             # Internal role variables
    ├── debian_11.yml        # Debian 11 specific variables
    ├── debian_12.yml        # Debian 12 specific variables
    ├── debian_13.yml        # Debian 13 specific variables
    ├── redhat_9.yml         # EL9 specific variables (Rocky/RHEL/Alma 9)
    ├── ubuntu_22.04.yml     # Ubuntu 22.04 specific variables
    ├── ubuntu_24.04.yml     # Ubuntu 24.04 specific variables
    └── ubuntu_26.04.yml     # Ubuntu 26.04 specific variables
```

## Example Playbook

```yaml
---
- name: Configure Docker Engine
  hosts: all
  become: true
  vars:
    docker_users:
      - "ubuntu"
    docker_daemon_config:
      log-driver: "json-file"
      log-opts:
        max-size: "10m"
        max-file: "3"
  roles:
    - role: grzegorzfranus.docker
```

## CI/CD Pipeline

This repository uses centralized, reusable GitHub Actions workflows from [grzegorzfranus/github-workflows](https://github.com/grzegorzfranus/github-workflows) (`v3.1.1`) for quality assurance, security scanning, and release automation.

### CI Pipeline (`ansible-ci.yml@v3.1.1`)

Runs on every Pull Request in a two-tier gate pattern:

1. **Branch Name Lint** — enforces naming conventions (`feature/`, `bugfix/`, `fix/`, `hotfix/`, `release/`, `chore/`, `docs/`, `refactor/`, `test/`, `build/`, `ci/`, `perf/`, `revert/`)
2. **PR Title Lint** — enforces [Conventional Commits](https://www.conventionalcommits.org/) format (`feat:`, `fix:`, `ci:`, etc.)
3. **YAML Syntax Lint** — validates YAML formatting via `yamllint`
4. **Ansible Lint** — checks Ansible best practices and role standards
5. **Galaxy Metadata & Variable Consistency Validation** — verifies `meta/main.yml` schema and enforces strict variable consistency across `defaults/main.yml`, `meta/argument_specs.yml`, `tasks/assert.yml`, and `README.md` in `vars-validation-mode: error` (`ansible-meta-validate.yml`)
6. **Security Scanning** — TruffleHog secret detection and Trivy IaC scanning (`ansible-security.yml`)
7. **Molecule Integration Tests** — executes Molecule test matrix across Ubuntu 26.04/24.04/22.04, Debian 13/12, and Rocky Linux 9 (`ansible-molecule.yml`). Four distinct scenarios are executed: `default` (core installation, service, socket, and daemon configuration), `logging` (rsyslog, logrotate, and OS default log owner fallback), `logging-override` (rsyslog log owner override, `copytruncate: false`, and `create` directive consistency), and `uninstall` (clean removal of packages, units, and configuration while preserving system logs).
8. **Merge Check Gate** — single authoritative status check aggregating all results for branch protection

### Release & Publish Pipeline (`ansible-publish.yml@v3.1.1`)

Automated via [Release Please](https://github.com/googleapis/release-please):

1. **Push to `main`** → Release Please creates or updates a Release PR with automated changelog generation
2. **Release PR Validation** → validates YAML syntax and actions schema before setting `Merge Check` status
3. **Merge Release PR** → creates Git version tag and GitHub Release automatically
4. **Ansible Galaxy Publish** → publishes tagged release to Ansible Galaxy via `ansible-publish.yml@v3.1.1` with exponential backoff retry logic

## 🤝 Contributing

Contributions, bug reports, and feature requests are welcome!

- Fork the repository and create your branch from `main`
- Use [Conventional Commits](https://www.conventionalcommits.org/) for commit messages
- Ensure your code passes all CI checks (YAML lint, Ansible lint, Molecule tests)
- Centralized workflows from [github-workflows](https://github.com/grzegorzfranus/github-workflows) version `v3.1.0` are used to run CI/CD pipelines
- Submit a pull request describing your changes (a template is available under `.github/PULL_REQUEST_TEMPLATE/pull_request_template.md` to help structure your PR description)
- For major changes, please open an issue first to discuss what you would like to change (issue templates for bug reports, feature requests, and tasks are available under `.github/ISSUE_TEMPLATE/`)

## 📝 License

This project is licensed under the Apache-2.0 License - see the LICENSE file for details.

## 👥 Author Information

This role was created by [Grzegorz Franus](https://github.com/grzegorzfranus).