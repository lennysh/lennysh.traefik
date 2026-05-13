# acme_dump

Role that reads a Traefik ACME JSON file (`acme.json`), walks resolver entries and their `Certificates` list, and writes PEM material and light metadata on the **control node** (where Ansible runs).

Expected JSON shape matches Traefik’s default storage: top-level object whose values include a `Certificates` array with base64-encoded `certificate` and `key` fields and a `domain` object (`main`, optional `sans`).

## Requirements

- Ansible `>=2.16` (role meta); collection requires `>=2.16.10` per collection `meta/runtime.yml`.
- The path `acme_dump_json_path` must exist and be readable by the task user.

## Role variables

Variables are defined in [`defaults/main.yml`](defaults/main.yml).

| Variable | Default | Description |
|----------|---------|-------------|
| `acme_dump_json_path` | `""` | Path to `acme.json`. Must be set (non-empty) for a meaningful run. |
| `acme_dump_export_dir` | `{{ playbook_dir }}/exported_certs` | Base directory for exports. Each cert gets a subdirectory. |
| `acme_dump_export_prefix_resolver` | `false` | If `true`, prefix directory names with the resolver key and `_` to avoid collisions between resolvers. |

## Exported layout

Under `acme_dump_export_dir`, one directory per certificate (safe name derived from `domain.main`, with `*.` turned into `_wildcard.`). Typical files:

| File | Mode | Contents |
|------|------|----------|
| `fullchain.pem` | `0644` | Full chain from Traefik’s stored certificate |
| `cert.pem` | `0644` | Leaf certificate (first PEM block) |
| `privkey.pem` | `0600` | Private key |
| `domain.txt` | `0644` | `main`, `sans`, and `resolver` (no secrets) |

## Example playbook

```yaml
---
- hosts: localhost
  connection: local
  gather_facts: false
  collections:
    - lennysh.traefik
  vars:
    acme_dump_json_path: /var/lib/traefik/acme.json
    acme_dump_export_dir: /tmp/traefik-export
    acme_dump_export_prefix_resolver: true
  roles:
    - role: lennysh.traefik.acme_dump
```

## Tags / Galaxy

See [`meta/main.yml`](meta/main.yml) for Galaxy metadata.

## License

MIT
