# NetDevOps - Ansible Network Automation

Ansible automation for Cisco IOS network labs. Covers two progressive lab scenarios: OSPF-only routing and a full MPLS/VPN service provider topology.

---

## Repository Structure

```
ansible/
├── ospf/          # Lab 1: OSPF baseline topology (5 routers)
├── mpls/          # Lab 2: MPLS/LDP/BGP VPNv4 topology (5 routers + 2 switches)
└── backups/       # Device config backups (date-stamped directories)
    └── YYYY-MM-DD/
        ├── R1.cfg ... R5.cfg
        ├── SW1.cfg, SW2.cfg
        └── README.txt
```

---

## Lab 1: OSPF (`ospf/`)

A 5-router Cisco IOS topology running OSPF Area 0.

**Inventory:** R1–R5 (`172.16.92.101–105`)

**Playbooks:**

| Playbook | Description |
|---|---|
| `ospf.yml` | Configure interfaces and OSPF on all routers (R1/R2 get explicit DR priority) |
| `save_configs.yml` | Save running-config to startup-config and back up to disk |

---

## Lab 2: MPLS (`mpls/`)

A service provider topology with an MPLS core, LDP, and BGP VPNv4 for two customer VRFs.

**Inventory:**

| Group | Hosts | IPs |
|---|---|---|
| `core` | R1, R2, R3 | `172.16.92.101–103` |
| `edge` (PE routers) | R4, R5 | `172.16.92.104–105` |
| `customer_prem` (CE switches) | SW1, SW2 | `172.16.92.201–202` |

**Topology summary:**

```
SW1 (CUST-A vlan10, CUST-B vlan20)
 |
R4 (PE) --- R1 --- R2 --- R5 (PE) --- SW2
             \           /
              --- R3 ---
```

- Core routers (R1–R3): OSPF Area 0 + MPLS LDP autoconfig
- Edge/PE routers (R4, R5): OSPF + MPLS + VRF (CUST-A `RD 1:1`, CUST-B `RD 2:2`) + iBGP VPNv4 (`AS 65450`)
- CE switches (SW1, SW2): VLANs 10 and 20, access ports toward PE subinterfaces

**Playbooks:**

| Playbook | Description |
|---|---|
| `ospf.yml` | Configure interfaces and OSPF on all devices |
| `mpls.yml` | Configure MPLS/LDP, VRFs, BGP VPNv4; verify OSPF/BGP adjacency and VPNv4 summary |
| `save_configs.yml` | Save startup-config on all devices and back up to `backups/YYYY-MM-DD/` |

**Templates (`templates/`):**

| Template | Used by |
|---|---|
| `ospf.j2` | Interfaces + OSPF (ospf lab) |
| `mpls.j2` | Interfaces + OSPF + MPLS LDP + VRFs + BGP VPNv4 |
| `vlans.j2` | VLANs and access port assignments on CE switches |

---

## Prerequisites

- Ansible with `cisco.ios` collection installed:
  ```bash
  ansible-galaxy collection install cisco.ios
  ```
- Ansible Vault for credential management. Each lab has:
  - `group_vars/all/vault.yml` — encrypted `vault_password` and `enable_password`
  - `.vault_pass` — plaintext vault password file (excluded from git via `.gitignore`)

---

## Usage

From within the `ospf/` or `mpls/` directory:

```bash
# Configure the full topology
ansible-playbook playbooks/ospf.yml
ansible-playbook playbooks/mpls.yml

# Back up all device configs
ansible-playbook playbooks/save_configs.yml

```

Backups are written to `ansible/backups/YYYY-MM-DD/` with a `README.txt` summary.

---

## Security Notes

- Credentials are stored in Ansible Vault (`group_vars/all/vault.yml`).
- `.vault_pass` and backup configs are excluded from version control via `.gitignore`.
- Never commit plaintext credentials or `.vault_pass` files.
