# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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

### Removed ❌
- Oracle Linux 8/9 from supported platforms
