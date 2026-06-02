cat > README.md << 'README'
<div align="center">

# 🖥️ lab-architecture

### Physical Enterprise Network Homelab

*Built from scratch while working full time*
*Phenix City, AL → Breaking into IT*

---

![Status](https://img.shields.io/badge/Status-Building-yellow?style=for-the-badge)
![Rack](https://img.shields.io/badge/Rack-16U-blue?style=for-the-badge)
![Firewall](https://img.shields.io/badge/Firewall-SonicWall_SOHO_250-red?style=for-the-badge)
![Router](https://img.shields.io/badge/Router-Cisco_ISR_1100-blue?style=for-the-badge)
![Switch](https://img.shields.io/badge/Switch-Managed_PoE%2B-green?style=for-the-badge)
![L3 Switch](https://img.shields.io/badge/L3_Switch-Cisco_3560--CX-blue?style=for-the-badge)
![Hypervisor](https://img.shields.io/badge/Hypervisor-Proxmox-orange?style=for-the-badge)
![OS](https://img.shields.io/badge/OS-Fedora_+_Endeavour-purple?style=for-the-badge)

</div>

---

## 👤 About

I'm Jamari — a 23 year old IT professional in training
based in Phenix City, AL. I built this physical enterprise
homelab completely from scratch while working full time,
using my own money and personal time to develop real IT
and networking skills.

My goal is to break into IT support and work my way
toward becoming a network engineer. Everything in this
repo is documented as I build and configure it — real
problems, real solutions, real configs.

---

## 📸 The Build

![Homelab Rack](build-log/photos/rack-overview.jpg)

---

## 🔧 Hardware Stack

### 🔴 Network Infrastructure
| Device | Model | Role | IP |
|--------|-------|------|----|
| 🔥 Firewall | SonicWall SOHO 250 | Gateway, VLANs, NAT, DPI | 10.10.10.1 |
| 🌐 Router | Cisco ISR 1100-4G | IOS XE, OSPF, ACLs | 10.10.10.2 |
| 🔀 Core Switch | Linksys LGS328PC | 24-port managed PoE+ | 10.10.10.3 |
| 🔀 CCNA Switch | Cisco Catalyst 3560-CX | Layer 3, CCNA practice | 10.10.10.4 |
| 📋 Patch Panel | LYSYMIXS 24-port CAT6 | Cable termination | — |
| ⚡ PDU | Rack power strip | Power distribution | — |

### 🟠 Compute
| Device | Model | Role | IP |
|--------|-------|------|----|
| 💻 Hypervisor | HP ProDesk | Proxmox VE | 10.20.20.10 |
| 📊 Monitoring | HP EliteDesk | Grafana + Prometheus | 10.20.20.11 |

### 🟣 Services — Raspberry Pi Cluster
| Device | Model | Role | IP |
|--------|-------|------|----|
| 🔐 VPN | Raspberry Pi 5 | WireGuard + Tailscale | 10.30.30.10 |
| 🛡️ DNS | Raspberry Pi 5 | Pi-hole + Wazuh agent | 10.30.30.11 |
| 📈 Dashboard | Raspberry Pi 4 + Elecrow 7in | Grafana dashboard | 10.30.30.12 |

### 🟢 Wireless
| Device | Model | Role | IP |
|--------|-------|------|----|
| 📶 Node 1 | Linksys MX2000 | Your room — lab WiFi VLAN 40 | 10.40.40.2 |
| 📶 Node 2 | Linksys MX2000 | Mom's room — mesh node | 10.40.40.3 |

### 🟡 Management Station
| Device | OS | Role |
|--------|----|------|
| 💻 MacBook Pro | Endeavour OS + Hyprland | SSH, console, Obsidian docs |
| 🖥️ Gaming PC | Fedora 43 + Hyprland | Main workstation |

---

## 🌐 Network Design

### VLAN Segmentation
| VLAN | Name | Subnet | Gateway | Purpose |
|------|------|--------|---------|---------|
| 10 | Management | 10.10.10.0/24 | 10.10.10.1 | Network gear admin only |
| 20 | Servers | 10.20.20.0/24 | 10.20.20.1 | Proxmox, VMs, EliteDesk |
| 30 | Services | 10.30.30.0/24 | 10.30.30.1 | Raspberry Pi cluster |
| 40 | Lab WiFi | 10.40.40.0/24 | 10.40.40.1 | Wireless lab devices |
| 50 | Workstations | 10.50.50.0/24 | 10.50.50.1 | Gaming PC, wired clients |
| 99 | Guest | 10.99.99.0/24 | 10.99.99.1 | Isolated guest devices |

### Network Flow
[Internet / ISP]
↓
[ARRIS Router] — 192.168.1.0/24
↓
[SonicWall SOHO 250] — Firewall / Gateway
↓
[Cisco ISR 1100-4G] — IOS XE Router
↓
[Linksys LGS328PC] — 24-port Managed PoE+
↓
[All Lab Devices]
Family network isolated at SonicWall

### Firewall Rules
| From | To | Action |
|------|----|--------|
| Family network | Lab VLANs | ❌ DENY |
| VLAN 99 Guest | All lab | ❌ DENY |
| VLAN 50 Workstations | VLAN 10 Mgmt | ✅ ALLOW |
| VLAN 20 Servers | VLAN 30 Services | ✅ ALLOW |
| WireGuard VPN | All VLANs | ✅ ALLOW |
| Any | WAN | ✅ ALLOW |

---

## 🗄️ Rack Layout
┌─────────────────────────────────┐
│ U1  │ LYSYMIXS Patch Panel      │
├─────────────────────────────────┤
│ U2  │ Linksys LGS328PC          │
│     │ 24-port Managed PoE+      │
├─────────────────────────────────┤
│ U3  │ Shelf → Cisco ISR 1100-4G │
├─────────────────────────────────┤
│ U4  │ Shelf → SonicWall SOHO250 │
├─────────────────────────────────┤
│ U5  │ Shelf → Cisco 3560-CX     │
├─────────────────────────────────┤
│ U6  │ Shelf → Pi Cluster        │
│     │ Pi5#1 + Pi5#2 + Pi4       │
├─────────────────────────────────┤
│ U7  │ PDU                       │
├─────────────────────────────────┤
│ U8-U16 │ Open — future gear     │
└─────────────────────────────────┘

---

## 🚀 Services

| Service | Platform | Status | Purpose |
|---------|----------|--------|---------|
| Active Directory | virt-manager | ✅ Running | User and GPO management |
| osTicket | virt-manager | ✅ Running | Help desk ticketing |
| Proxmox VE | HP ProDesk | 🔄 Installing | Virtualization |
| Pi-hole | Raspberry Pi 5 | 🔄 Pending | Network DNS filtering |
| WireGuard | Raspberry Pi 5 | 🔄 Pending | Remote VPN access |
| Tailscale | Raspberry Pi 5 | 🔄 Pending | Mesh VPN |
| Grafana | Raspberry Pi 4 | 🔄 Pending | Network monitoring |
| Prometheus | HP EliteDesk | ✅ Running | Metrics collection |
| Wazuh SIEM | Proxmox VM | 🔄 Pending | Security monitoring |

---

## 📝 Build Log

| Day | Date | Milestone |
|-----|------|-----------|
| Day 1 | May 2025 | Rack assembled, casters mounted |
| Day 2 | May 2025 | Patch panel U1, switch U2 mounted |
| Day 3 | May 2025 | Shelves, Cisco ISR, SonicWall placed |
| Day 4 | May 2025 | PDU, Pi cluster, Proxmox install |

---

## 🛠️ Skills Demonstrated

**Networking**
- Designed 6-VLAN enterprise network topology
- Configured SonicWall enterprise firewall with DPI
- Cisco IOS XE routing, OSPF, and ACL configuration
- Managed PoE+ switch VLAN and trunking
- Layer 3 switching with Cisco 3560-CX
- WireGuard and VPN configuration
- Network segmentation and isolation

**Systems**
- Proxmox hypervisor deployment and VM management
- Windows Server 2022 Active Directory
- Linux administration — Fedora, Endeavour, Pi OS
- Raspberry Pi cluster with PoE+ power management

**Security**
- Firewall rule design and NAT policies
- VLAN isolation between networks
- Wazuh SIEM deployment
- VPN remote access configuration

**Documentation**
- GitHub runbook with full build documentation
- Obsidian knowledge base with device linking
- Network diagrams and topology maps
- Troubleshooting log with root cause analysis

---

## 🎓 Certifications

| Cert | Status | Expected |
|------|--------|----------|
| Google IT Support Certificate | 🔄 In Progress | 2026 |
| Cisco CCNA 200-301 | 🔄 In Progress | 2026 |

---

## 🔗 Related Projects

- **[Virtual Lab](https://github.com/SilentStacker/IT-Help-Desk-Active-Directory-Lab)** — Active Directory, osTicket, GPO policies on virt-manager

---

<div align="center">

---

*Built solo in Phenix City, AL*
*Working full time → Breaking into IT*

[![GitHub](https://img.shields.io/badge/GitHub-SilentStacker-black?style=for-the-badge&logo=github)](https://github.com/SilentStacker)

</div>