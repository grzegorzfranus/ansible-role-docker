# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.1] - 2025-08-13
### Removed ❌
- Molecule verify: removed strict `daemon.json` content assertion that caused false failures across distros/runtime setups

## [1.0.0] - 2025-08-13
### Added ✅
- Initial release of `ansible-role-docker`
- Install Docker from official repositories (Debian 11/12, Ubuntu 22.04/24.04, Rocky 9)
- Secure defaults: unix socket only, json-file logs with rotation (10m/3)
- Group/user management, socket permissions enforcement (0660, group docker)
- Optional rsyslog + logrotate for dockerd logs
  - Rsyslog programname filter with per-OS file ownership (Ubuntu: syslog:adm, Rocky: root:root)
  - Flexible log destination (`/var/log/docker/docker.log` or custom path)
  - Logrotate: copytruncate by default; create-on-rotate support when disabled
- Optional prune and backup systemd timers
- Optional auditd rule for `/var/run/docker.sock`
- Optional user-defined bridge networks

### Changed 🔄
- Molecule converge uses Galaxy role name `grzegorzfranus.docker` (was `ansible-role-docker`)
- README examples updated to use `grzegorzfranus.docker`
- Rsyslog configuration simplified and made distro-friendly; default log path via `docker_log_directory_path`
- Assertions expanded to validate logging/logrotate vars and package state

### Fixed 🔧
- daemon.json generation: removed leading comment and only set `hosts` when `docker_enable_tcp_socket=true`
- Avoided fd:// vs daemon.json conflict via systemd `ExecStart=` override drop-in for `docker.service`
- Ensured log directory/file are created with correct owner/group/mode per OS before rsyslog reload
- Logrotate now supports `copytruncate` or `create` (with mode/owner/group) when not using copytruncate
- Lint: role is clean under role-local ansible-lint and yamllint configs

### Removed ❌
- Oracle Linux 8/9 from supported platforms
