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
| Ubuntu | 24.04 (Noble) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Ubuntu | 22.04 (Jammy) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 12 (Bookworm) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 11 (Bullseye) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Rocky Linux | 9 | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| RHEL | 9 | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| AlmaLinux | 9 | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |

### Ansible version

Ansible >= 2.16

### Python version

Python >= 3.9

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
docker_enhanced_error_reporting: true
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

### General Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_service_enabled` | Enable/disable Docker service on boot | `true` |
| `docker_package_state` | Package state for Docker packages (`present` or `latest`) | `"present"` |
| `docker_users` | List of users to add to `docker` group | `[]` |

### Packages and Plugins

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_install_compose_plugin` | Install `docker-compose-plugin` | `true` |
| `docker_install_buildx_plugin` | Install `docker-buildx-plugin` | `true` |
| `docker_extra_packages` | Additional package list to install | `[]` |

### Daemon Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_enable_tcp_socket` | Enable dockerd TCP socket (no TLS) | `false` |
| `docker_tcp_listen` | TCP listen address when enabled | `"0.0.0.0:2375"` |
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
| `docker_log_directory_path` | Log directory path | `"/var/log/docker"` |
| `docker_rsyslog_conf_name` | Name of rsyslog config file in `/etc/rsyslog.d/` | `"99-docker.conf"` |
| `docker_rsyslog_imjournal_filecreatemode` | imjournal file creation mode | `"0644"` |
| `docker_rsyslog_log_file_owner` | Owner of the docker log file (per-OS overrides) | `"root"` |
| `docker_rsyslog_log_file_group` | Group of the docker log file (per-OS overrides) | `"adm"` |
| `docker_rsyslog_log_file_mode` | Mode of the docker log file (per-OS overrides) | `"0640"` |
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
| `docker_logrotate_create.owner` | New file owner (when creating) | `"root"` (per-OS overrides) |
| `docker_logrotate_create.group` | New file group (when creating) | `"adm"` (per-OS overrides) |

Note: On Ubuntu, the role sets log file owner/group to `syslog:adm`; on EL9 it uses `root:root`. See OS-specific vars in `vars/`.

### Prune Timer

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_prune_enabled` | Enable systemd prune timer | `false` |
| `docker_prune_frequency` | Prune cadence (`daily`, `weekly`, `monthly`) | `"weekly"` |
| `docker_prune_flags` | Prune flags | `"--all --volumes --force"` |

### Backup Timer

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_backup_enabled` | Enable systemd backup timer | `false` |
| `docker_backup_path` | Backup destination directory | `"/var/backups/docker"` |
| `docker_backup_retention` | Number of retained backups | `7` |
| `docker_backup_include_volumes_metadata` | Include volumes list and metadata | `false` |

### Rootless Mode

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_rootless_enabled` | Enable rootless Docker (opt-in, future enhancement) | `false` |
| `docker_rootless_users` | Users to configure for rootless | `[]` |

### Networks

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_networks` | List of user-defined networks to create (e.g. `{ name, driver, subnet }`) | `[]` |

### Audit

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_audit_socket` | Add auditd rule for `/var/run/docker.sock` if auditd present | `false` |

### System Validation Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_validate_system` | Enable comprehensive system validation checks | `true` |
| `docker_validate_connectivity` | Enable network connectivity verification to Docker repositories | `true` |
| `docker_validate_prerequisites` | Enable architecture and package manager validation | `true` |
| `docker_collect_system_info` | Collect detailed system information for diagnostics | `true` |
| `docker_connectivity_timeout` | Timeout for network connectivity checks (seconds) | `10` |

### Error Reporting Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| `docker_enhanced_error_reporting` | Enable enhanced error reporting with system diagnostics | `true` |
| `docker_include_hardware_info` | Include system hardware information in error reports | `true` |
| `docker_include_network_info` | Include network configuration in error reports | `true` |
| `docker_include_package_info` | Include package manager state in error reports | `true` |
| `docker_error_log_lines` | Maximum lines of log output to include in error reports | `50` |

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
- ✅ **Systemd Overrides**: ExecStart override prevents flag conflicts
- ✅ **Audit Support**: Optional auditd rule for docker.sock monitoring
- ✅ **Proxy Support**: Configurable HTTP/HTTPS proxy via systemd drop-ins
- ✅ **Minimal Attack Surface**: TCP socket disabled by default

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
- `setup` - Setup and configuration tasks
- `init` - Initial environment setup and variable loading
- `validate` - Variable validation and system checks
- `requirements` - Prerequisite and repository configuration tasks
- `install` - Package installation tasks
- `configure` - Service and daemon configuration tasks
- `service` - Service management tasks
- `logging` - rsyslog and logrotate configuration
- `prune` - Prune timer tasks
- `backup` - Backup timer tasks
- `audit` - Audit configuration tasks

## 📁 File Structure

```
ansible-role-docker/
├── .github/                  # GitHub Actions workflows
│   └── workflows/           # CI/CD automation
│       ├── ci.yml           # CI pipeline (reusable ansible-ci.yml)
│       └── release.yml      # Release Please + Galaxy publish
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
│   └── default/
│       ├── molecule.yml     # Test configuration
│       ├── converge.yml     # Role execution playbook
│       ├── prepare.yml      # Test preparation tasks
│       └── verify.yml       # Verification tests
├── tasks/
│   ├── main.yml             # Main task orchestration
│   ├── assert.yml           # Variable validation
│   ├── validate.yml         # System validation and diagnostics
│   ├── error_reporting.yml  # Enhanced error reporting
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
    ├── redhat_9.yml         # EL9 specific variables (Rocky/RHEL/Alma 9)
    ├── ubuntu_22.04.yml     # Ubuntu 22.04 specific variables
    └── ubuntu_24.04.yml     # Ubuntu 24.04 specific variables
```

## CI/CD Pipeline

### CI Pipeline

Runs on every Pull Request via centralized reusable workflow:

1. **Branch Name Lint** — enforces naming conventions (`feature/`, `bugfix/`, etc.)
2. **PR Title Lint** — enforces Conventional Commits naming conventions on PR titles
3. **YAML Lint** — validates all YAML files
4. **Ansible Lint** — enforces best practices and guidelines compliance
5. **Security Scan** — TruffleHog secret detection
6. **Molecule Tests** — matrix across Debian 11/12, Ubuntu 22.04/24.04, and Rocky Linux 9
7. **Merge Check** — caller-level gate for branch protection

### Release & Publish

Automated via [Release Please](https://github.com/googleapis/release-please):

1. Merge to `main` → Release Please creates a Release PR with changelog
2. Merge Release PR → creates Git tag + GitHub Release
3. Galaxy publish triggers automatically on release using centralized action

## 🤝 Contributing

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
- For major changes, please open an issue first to discuss what you would like to change

## 📝 License

This project is licensed under the Apache-2.0 License - see the LICENSE file for details.

## 👥 Author Information

This role was created by [Grzegorz Franus](https://github.com/grzegorzfranus).