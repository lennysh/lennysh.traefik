# lennysh.traefik

Ansible collection for working with [Traefik](https://traefik.io/). It includes tooling to read Traefik’s JSON ACME store (`acme.json`) and export certificates and keys as PEM files on the Ansible control node.

## Requirements

- **Ansible:** `>=2.16.10` (see [`meta/runtime.yml`](meta/runtime.yml))

## Installation

Directly from this repository:

```bash
ansible-galaxy collection install git+https://github.com/lennysh/lennysh.traefik.git
```

After the collection is published to Ansible Galaxy:

```bash
ansible-galaxy collection install lennysh.traefik
```

From a local checkout of this repository (collection root = repo root):

```bash
ansible-galaxy collection build .
ansible-galaxy collection install lennysh-traefik-*.tar.gz
```

For iterative development without building a tarball, you can add the collection to [`ANSIBLE_COLLECTIONS_PATH`](https://docs.ansible.com/ansible/latest/reference_appendices/config.html#collections-paths) so that the `lennysh.traefik` namespace resolves to your working tree.

## Content

| Kind   | Name        | Description |
|--------|-------------|-------------|
| Role   | `acme_dump` | Parse Traefik `acme.json` and write `fullchain.pem`, `cert.pem`, `privkey.pem`, and `domain.txt` per certificate. |

Role details: [`roles/acme_dump/README.md`](roles/acme_dump/README.md).

Custom plugins: none yet; see [`plugins/README.md`](plugins/README.md).

## Example playbook

```yaml
---
- name: Export Traefik ACME certificates to PEM files
  hosts: localhost
  connection: local
  gather_facts: false
  collections:
    - lennysh.traefik
  vars:
    acme_dump_json_path: /path/to/acme.json
    acme_dump_export_dir: /path/to/exported_certs
  roles:
    - role: lennysh.traefik.acme_dump
```

## Playbook in this repository

The example playbook calls the role by FQCN (`lennysh.traefik.acme_dump`). Install the collection first (from the repository root):

```bash
ansible-galaxy collection install .
```

Then run:

```bash
ansible-playbook playbooks/dump_acme_certs.yml
```

Override paths with extra vars, for example:

```bash
ansible-playbook playbooks/dump_acme_certs.yml \
  -e acme_dump_json_path=/path/to/acme.json \
  -e acme_dump_export_dir=/path/to/out
```

## License

MIT (see [`galaxy.yml`](galaxy.yml)).

## Links

- **Repository:** https://github.com/lennysh/lennysh.traefik (also set in [`galaxy.yml`](galaxy.yml) as `repository`).
