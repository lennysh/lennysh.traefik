# systemd units

These units run the collection playbook [`lennysh.traefik.dump_acme_certs`](https://galaxy.ansible.com/ui/repo/published/lennysh/traefik/) on a schedule so Traefik `acme.json` is exported to PEM files without manual runs.

| File | Purpose |
|------|---------|
| `traefik-acme-dump.service` | One-shot: `ansible-playbook lennysh.traefik.dump_acme_certs` with the configured `-e` paths. |
| `traefik-acme-dump.timer` | Fires the service at the start of every wall-clock hour (`OnCalendar=*-*-* *:00:00`). |

## Prerequisites

- `ansible-playbook` at the path in the service file (default: `/usr/local/bin/ansible-playbook`), or edit `ExecStart`.
- The **lennysh.traefik** collection installed where that Ansible can find it (system paths, or set `Environment=ANSIBLE_COLLECTIONS_PATH=...` under `[Service]`).
- Read access to `acme_dump_json_path` and write access to `acme_dump_export_dir` (defaults in the unit: `/certs/le-cloudflare.json` and `/certs/exported`). Adjust the `-e` arguments in the service file if your paths differ.

## Install (system units)

From the repository root (or use full paths to the unit files):

```bash
sudo cp systemd/traefik-acme-dump.service systemd/traefik-acme-dump.timer /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable --now traefik-acme-dump.timer
```

## Useful commands

```bash
# Next run and last result
systemctl list-timers traefik-acme-dump.timer

# Timer and service status
systemctl status traefik-acme-dump.timer
systemctl status traefik-acme-dump.service

# Logs from playbook runs
journalctl -u traefik-acme-dump.service -f

# Run once immediately (does not change the hourly schedule)
sudo systemctl start traefik-acme-dump.service
```

To stop scheduling but keep the units installed:

```bash
sudo systemctl disable --now traefik-acme-dump.timer
```

## Customization

- **Paths and playbook**: Edit `ExecStart=` in `traefik-acme-dump.service` (inventory, extra vars, or a playbook path on disk).
- **Schedule**: Edit `OnCalendar=` in `traefik-acme-dump.timer` ([systemd.time(7)](https://www.freedesktop.org/software/systemd/man/latest/systemd.time.html)). For “every hour since the last run” instead of wall clock, consider `OnUnitActiveSec=1h` plus `OnBootSec=…`.
- **User**: Add `User=` and `Group=` under `[Service]` if the job should not run as root.
- **User timer**: To run as your login without sudo for `systemctl --user`, copy the same files to `~/.config/systemd/user/`, run `systemctl --user daemon-reload`, then `systemctl --user enable --now traefik-acme-dump.timer`, and enable lingering if the timer must run while you are logged out: `loginctl enable-linger "$USER"`.

After any edit to the unit files under `/etc/systemd/system/`, run `sudo systemctl daemon-reload` and restart the timer if it is already enabled.
