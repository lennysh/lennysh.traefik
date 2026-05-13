# Changelog

All notable changes to this Ansible collection (**lennysh.traefik**) are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0-devel]

### Added

- Initial collection with the `acme_dump` role to parse Traefik `acme.json` and export PEM certificates and keys on the control node.

### Changed

- **acme_dump** role variables renamed for ansible-lint `var-naming[no-role-prefix]`: `traefik_acme_json_path` → `acme_dump_json_path`, `traefik_acme_export_dir` → `acme_dump_export_dir`, `traefik_acme_export_prefix_resolver` → `acme_dump_export_prefix_resolver`.
