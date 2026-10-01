# MPLS Lab - Ansible Automation

Ansible automation for a Cisco IOS MPLS/LDP/BGP VPNv4 service provider lab topology.

## Topology

- **Core routers (R1–R3):** OSPF Area 0, MPLS LDP autoconfig
- **PE routers (R4, R5):** OSPF + MPLS + VRF + iBGP VPNv4 (AS 65450)
- **CE switches (SW1, SW2):** VLANs 10/20, access ports to PE subinterfaces

**VRFs:** `CUST-A` (RD 1:1) and `CUST-B` (RD 2:2)

## Playbooks

| Playbook | Description |
|---|---|
| `ospf.yml` | Configure interfaces and OSPF on all devices |
| `mpls.yml` | Configure MPLS, VRFs, BGP VPNv4; verify adjacencies |
| `save_configs.yml` | Save to startup-config and back up to `backups/YYYY-MM-DD/` |


## Quick Start

```bash
cd mpls/
ansible-playbook playbooks/ospf.yml
ansible-playbook playbooks/mpls.yml
ansible-playbook playbooks/save_configs.yml
```

See the top-level [README](../README.md) for full details.
