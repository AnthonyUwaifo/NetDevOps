# OSPF Lab - Ansible Automation

Ansible automation for a Cisco IOS OSPF lab topology.

## Topology

- **Routers (R1–R5):** OSPF Area 0
- R1 DR priority: 150, R2 DR priority: 100

## Playbooks

| Playbook | Description |
|---|---|
| `ospf.yml` | Configure interfaces and OSPF on all routers (R1/R2 get explicit DR priority) |
| `save_configs.yml` | Save to startup-config and back up to `backups/YYYY-MM-DD/` |

## Quick Start

```bash
cd ospf/
ansible-playbook playbooks/ospf.yml
ansible-playbook playbooks/save_configs.yml
```

See the top-level [README](../README.md) for full details.
