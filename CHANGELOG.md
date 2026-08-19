# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.5.0](https://github.com/grzegorzfranus/ansible-role-docker/compare/v1.4.1...v1.5.0) (2026-08-19)


### Features

* **logrotate:** make dateformat configurable ([#39](https://github.com/grzegorzfranus/ansible-role-docker/issues/39)) ([bd44761](https://github.com/grzegorzfranus/ansible-role-docker/commit/bd44761bfaa5d910126ba62e7eb92283575cfcb8))

## [1.4.1](https://github.com/grzegorzfranus/ansible-role-docker/compare/v1.4.0...v1.4.1) (2026-08-07)


### Code Refactoring

* **apt:** resolve INJECT_FACTS_AS_VARS and migrate to deb822_repository ([#36](https://github.com/grzegorzfranus/ansible-role-docker/issues/36)) ([ec4a981](https://github.com/grzegorzfranus/ansible-role-docker/commit/ec4a9810e80b154d00f822900576717fdf29c65e))

## [1.4.0](https://github.com/grzegorzfranus/ansible-role-docker/compare/v1.3.1...v1.4.0) (2026-07-30)


### Features

* add support for Ubuntu 26.04 (Resolute) and Debian 13 (Trixie) ([#33](https://github.com/grzegorzfranus/ansible-role-docker/issues/33)) ([3f4d88d](https://github.com/grzegorzfranus/ansible-role-docker/commit/3f4d88d31b6b44a13428bd228d617d77387044d3))

## [1.3.1](https://github.com/grzegorzfranus/ansible-role-docker/compare/v1.3.0...v1.3.1) (2026-07-30)


### CI/CD

* adopt variable consistency validation in error mode ([#30](https://github.com/grzegorzfranus/ansible-role-docker/issues/30)) ([e5c77cc](https://github.com/grzegorzfranus/ansible-role-docker/commit/e5c77cc95ecfcec78ec80fae1bc2efe832acd9d7))

## [1.3.0](https://github.com/grzegorzfranus/ansible-role-docker/compare/v1.2.6...v1.3.0) (2026-07-30)


### Features

* remove no-op variables and unreachable diagnostic scaffolding ([#27](https://github.com/grzegorzfranus/ansible-role-docker/issues/27)) ([32e423e](https://github.com/grzegorzfranus/ansible-role-docker/commit/32e423e5ba4997888017c43883e53760b26c3d40))

## [1.2.6](https://github.com/grzegorzfranus/ansible-role-docker/compare/v1.2.5...v1.2.6) (2026-07-30)


### Miscellaneous

* harden templates, extend validation and align with role standards ([#24](https://github.com/grzegorzfranus/ansible-role-docker/issues/24)) ([0937c19](https://github.com/grzegorzfranus/ansible-role-docker/commit/0937c19495988a4b1c0a09a104c1b0b505719573))

## [1.2.5](https://github.com/grzegorzfranus/ansible-role-docker/compare/v1.2.4...v1.2.5) (2026-07-30)


### Tests

* rewrite molecule verification with real assertions and add uninstall scenario ([#21](https://github.com/grzegorzfranus/ansible-role-docker/issues/21)) ([66c5ca7](https://github.com/grzegorzfranus/ansible-role-docker/commit/66c5ca765e9f219c3ba879e31af335187593dbdc))

## [1.2.4](https://github.com/grzegorzfranus/ansible-role-docker/compare/v1.2.3...v1.2.4) (2026-07-30)


### Bug Fixes

* correct dockerd ExecStart override, destructive uninstall and register handling ([#18](https://github.com/grzegorzfranus/ansible-role-docker/issues/18)) ([6dd9a2b](https://github.com/grzegorzfranus/ansible-role-docker/commit/6dd9a2bc837f906656929759140cbc8caa73fbaa))

## [1.2.3](https://github.com/grzegorzfranus/ansible-role-docker/compare/v1.2.2...v1.2.3) (2026-07-21)


### CI/CD

* **13:** upgrade github workflows to v3.0.1 and update documentation ([#14](https://github.com/grzegorzfranus/ansible-role-docker/issues/14)) ([cb907d4](https://github.com/grzegorzfranus/ansible-role-docker/commit/cb907d493606af92fcc68e8009a67aff02cc4f18))

## [1.2.2](https://github.com/grzegorzfranus/ansible-role-docker/compare/v1.2.1...v1.2.2) (2026-06-30)


### Miscellaneous

* migrate workflows to github-workflows and align configuration ([#10](https://github.com/grzegorzfranus/ansible-role-docker/issues/10)) ([d0fecc4](https://github.com/grzegorzfranus/ansible-role-docker/commit/d0fecc4fea4a8b99dc4a26bccb997710761e640e))

## [1.2.1](https://github.com/grzegorzfranus/ansible-role-docker/compare/v1.2.0...v1.2.1) (2026-05-24)


### Documentation

* add Troubleshooting and Example Playbook sections to README.md ([#7](https://github.com/grzegorzfranus/ansible-role-docker/issues/7)) ([45fb864](https://github.com/grzegorzfranus/ansible-role-docker/commit/45fb86474283c2fb64eeec62a969b353a0f941bd))

## [1.2.0](https://github.com/grzegorzfranus/ansible-role-docker/compare/v1.1.1...v1.2.0) (2026-05-24)


### Features

* **ci:** standardize ci/cd workflows and align configurations ([#5](https://github.com/grzegorzfranus/ansible-role-docker/issues/5)) ([ac751ae](https://github.com/grzegorzfranus/ansible-role-docker/commit/ac751ae4afe353561cf3a30266e8e71800f1b7a4))

## [1.1.1](https://github.com/grzegorzfranus/ansible-role-docker/compare/v1.1.0...v1.1.1) (2026-05-21)


### Code Refactoring

* align docker role with tailscale enterprise patterns ([#3](https://github.com/grzegorzfranus/ansible-role-docker/issues/3)) ([dc308b5](https://github.com/grzegorzfranus/ansible-role-docker/commit/dc308b590423fed7d030b5a9d27d55aada1ba633))

## [1.1.0](https://github.com/grzegorzfranus/ansible-role-docker/compare/v1.0.1...v1.1.0) (2026-05-21)


### Features

* migrate to centralized CI, Release Please, and Galaxy publish ([#1](https://github.com/grzegorzfranus/ansible-role-docker/issues/1)) ([26a9614](https://github.com/grzegorzfranus/ansible-role-docker/commit/26a96145d12a201bc28a74ed8d3ea87e289eb6f1))

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
