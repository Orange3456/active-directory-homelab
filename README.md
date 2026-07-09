# Active Directory Enterprise Homelab

![Active Directory](https://img.shields.io/badge/Active%20Directory-0078D4?style=for-the-badge&logo=microsoft&logoColor=white)
![Windows Server](https://img.shields.io/badge/Windows%20Server%202025-0078D4?style=for-the-badge&logo=windows&logoColor=white)
![PowerShell](https://img.shields.io/badge/PowerShell-5391FE?style=for-the-badge&logo=powershell&logoColor=white)
![VMware](https://img.shields.io/badge/VMware-607078?style=for-the-badge&logo=vmware&logoColor=white)
![OPNsense](https://img.shields.io/badge/OPNsense-D94F00?style=for-the-badge&logo=opnsense&logoColor=white)
![PKI](https://img.shields.io/badge/PKI%20%2F%20AD%20CS-00695C?style=for-the-badge&logo=letsencrypt&logoColor=white)

A comprehensive, multi-site Active Directory enterprise lab built entirely from scratch on a Dell Latitude (i5, 16GB RAM) using VMware Workstation. Every VM, every configuration, every command - done hands-on across 30+ chapters covering IAM, IT Infrastructure, Cloud Security and SOC.

---

## Table of Contents

- [Lab Architecture](#lab-architecture)
- [What Was Built](#what-was-built)
- [Screenshots](#screenshots)
  - [A - AD Structure and Users](#a---ad-structure-and-users)
  - [B - Multi-Site and Replication](#b---multi-site-and-replication)
  - [C - Group Policy and Security](#c---group-policy-and-security)
  - [D - File Server and Client Access](#d---file-server-and-client-access)
  - [E - PowerShell Automation](#e---powershell-automation)
  - [F - Monitoring and SOC](#f---monitoring-and-soc)
  - [G - PKI and LDAPS](#g---pki-and-ldaps)
  - [H - Backup and DHCP](#h---backup-and-dhcp)
- [Skills Demonstrated](#skills-demonstrated)
- [Tools and Technologies](#tools-and-technologies)
- [Repository Structure](#repository-structure)
- [About](#about)

---

## Lab Architecture

| Component | Details |
|-----------|---------|
| Domain | labshandson.lan |
| Forest | Single forest, single domain |
| Site 1 - Delhi HQ | 192.168.1.0/24 - VMnet2 |
| Site 2 - Mumbai Branch | 192.168.10.0/24 - VMnet3 |
| DC01 (Forest Root) | Win2K25-DC01 - 192.168.1.11 - Delhi HQ |
| DC02 (Redundant DC) | Win2K25-DC02 - 192.168.1.12 - Delhi HQ |
| MUMDC01 (Branch DC) | Win2K25-MUMDC01 - 192.168.10.11 - Mumbai Branch |
| Router / Firewall | OPNsense - 3 adapters (Delhi LAN / Mumbai LAN / WAN NAT) |
| Client | Win11-Client - 192.168.1.50 - Windows 11 domain-joined |
| Hardware | Dell Latitude i5, 16GB RAM, 512GB SSD |
| Virtualisation | VMware Workstation - all VMs thin-provisioned |

---

## What Was Built

### Active Directory Structure
- Planned and built a complete multi-site OU structure from scratch
- Delhi-HQ OU with Users, Computers and Groups sub-OUs
- Mumbai-Branch OU with identical sub-structure
- IT-Admins OU for privileged accounts separation
- Domain Controllers OU with all three DCs registered

### Identity and Access Management (IAM)
- Complete Joiner-Mover-Leaver (JML) lifecycle implemented hands-on
- Security groups with AGDLP best practice
- Group-based NTFS file share permissions on SalesDocs
- User properties configured: department, title, manager, email
- PowerShell bulk user creation, stale account detection, CSV-based offboarding

### IT Infrastructure
- DC01 promoted as forest root - new forest, new domain (labshandson.lan)
- DC02 joined as redundant Domain Controller - replication verified with repadmin
- MUMDC01 joined across OPNsense router - promoted as branch DC
- AD Sites and Services - Delhi-HQ and Mumbai-Branch sites with correct subnet associations
- FSMO roles transferred between DCs and verified with netdom query fsmo
- DHCP server role - Delhi-Workstations scope (192.168.1.100-200)
- File server with shared folders and NTFS permissions
- Windows 11 client domain-joined and tested end-to-end

### Security Hardening
- OPNsense firewall tightened to specific AD ports only (DNS 53, Kerberos 88, RPC 135, LDAP 389, SMB 445, LDAPS 636, Global Catalog 3268/3269, Dynamic RPC 49152-65535)
- Group Policy password policy (12 character minimum, complexity enabled, 90-day expiry)
- Account lockout policy configured
- System State backup to separate virtual disk
- AD CS - Enterprise Root CA deployed
- LDAPS enabled and verified on port 636 via ldp.exe
- Separate admin account model (IT-Admins OU)

### SOC and Monitoring
- Advanced audit policies enabled via auditpol (Logon, Account Management, Kerberos)
- Real Event ID 4624 (successful logon) captured and analysed
- Real Event ID 4625 (failed logon) captured via PowerShell Get-WinEvent
- Key SOC Event IDs understood: 4624, 4625, 4720, 4726, 4728, 4740, 4768, 4769, 4662
- Attack technique awareness: Password Spraying, Kerberoasting, DCSync, Pass-the-Hash, Golden Ticket

### PowerShell Automation
- Get-ADUser, New-ADUser, Set-ADUser, Disable-ADAccount, Remove-ADUser
- Get-ADGroupMember, Add-ADGroupMember, Remove-ADGroupMember
- Bulk user creation from array with foreach loop
- Stale account detection using LastLogonTimestamp
- Security event log querying with Get-WinEvent
- CSV-based bulk operations for mass onboarding and offboarding

---

## Screenshots

### A - AD Structure and Users

| | |
|---|---|
| ![A1](screenshots/A-AD-Structure/A1-ADUC-OU-Structure.png) | ![A2](screenshots/A-AD-Structure/A2-Delhi-HQ-Users.png) |
| A1 - Full OU structure: Delhi-HQ, Mumbai-Branch, IT-Admins | A2 - Delhi-HQ user accounts in Users OU |
| ![A3](screenshots/A-AD-Structure/A3-User-Properties-GroupMembership.png) | ![A5](screenshots/A-AD-Structure/A5-Three-Domain-Controllers.png) |
| A3 - User properties showing Delhi-Sales-Team group membership | A5 - All three Domain Controllers registered in the domain |

---

### B - Multi-Site and Replication

| | |
|---|---|
| ![B1](screenshots/B-MultiSite-Replication/B1-AD-Sites-Services-MultiSite.png) | ![B2](screenshots/B-MultiSite-Replication/B2-Repadmin-ReplSummary-Zero-Failures.png) |
| B1 - AD Sites and Services: Delhi-HQ and Mumbai-Branch with subnets | B2 - repadmin /replsummary showing 0 failures across all 3 DCs |
| ![B3](screenshots/B-MultiSite-Replication/B3-OPNsense-AD-Firewall-Rules.png) | ![B4](screenshots/B-MultiSite-Replication/B4-FSMO-Roles-Netdom.png) |
| B3 - OPNsense firewall rules tightened to specific AD ports | B4 - FSMO roles verified with netdom query fsmo |

---

### C - Group Policy and Security

| | |
|---|---|
| ![C1](screenshots/C-Group-Policy-Security/C1-Group-Policy-Management-Console.png) | ![C2](screenshots/C-Group-Policy-Security/C2-Password-Policy-GPO-Settings.png) |
| C1 - GPMC showing Delhi-Password-Policy linked to Delhi-HQ OU | C2 - Password policy: 12 char minimum, complexity enabled, 90-day expiry |

---

### D - File Server and Client Access

| | |
|---|---|
| ![D1](screenshots/D-FileServer-Client/D1-SalesDocs-Network-Share-Access.png) | ![D2](screenshots/D-FileServer-Client/D2-Mapped-Network-Drive-S.png) |
| D1 - SalesDocs share accessed from Win11 client as praveen.kumar | D2 - S: drive mapped persistently on domain-joined Windows 11 client |
| ![D3](screenshots/D-FileServer-Client/D3-NTFS-Permissions-SalesDocs.png) | |
| D3 - NTFS permissions showing Delhi-Sales-Team group with Modify access | |

---

### E - PowerShell Automation

| | |
|---|---|
| ![E1](screenshots/E-PowerShell-Automation/E1-PowerShell-Get-ADUser.png) | ![E2](screenshots/E-PowerShell-Automation/E2-PowerShell-Bulk-User-Creation.png) |
| E1 - Get-ADUser listing all domain accounts with enabled status | E2 - Bulk user creation script with foreach loop and green output |
| ![E3](screenshots/E-PowerShell-Automation/E3-PowerShell-Stale-Account-Detection.png) | |
| E3 - Stale account detection using LastLogonTimestamp filter | |

---

### F - Monitoring and SOC

| | |
|---|---|
| ![F1](screenshots/F-Monitoring-SOC/F1-Security-Event-Log.png) | ![F2](screenshots/F-Monitoring-SOC/F2-Event-4624-Successful-Logon.png) |
| F1 - Windows Security Event Log on DC01 with audit events | F2 - Event ID 4624: successful logon for praveen.kumar via Kerberos |
| ![F3](screenshots/F-Monitoring-SOC/F3-Event-4625-Failed-Logon-PowerShell.png) | |
| F3 - Event ID 4625: failed logon captured via PowerShell Get-WinEvent | |

---

### G - PKI and LDAPS

| | |
|---|---|
| ![G1](screenshots/G-PKI-LDAPS/G1-AD-Certificate-Services-CA.png) | ![G2](screenshots/G-PKI-LDAPS/G2-LDAPS-Port-636-Verified.png) |
| G1 - AD Certificate Services: Enterprise Root CA with issued DC certificate | G2 - LDAPS connection verified on port 636 via ldp.exe (Error 0 = success) |

---

### H - Backup and DHCP

| | |
|---|---|
| ![H1](screenshots/H-Backup-DHCP/H1-System-State-Backup-Complete.png) | ![H2](screenshots/H-Backup-DHCP/H2-DHCP-Scope-Active-Leases.png) |
| H1 - System State backup to separate virtual disk (E:) | H2 - DHCP scope Delhi-Workstations active with address leases |

---

## Skills Demonstrated

| Role | Skills Covered in This Lab |
|------|---------------------------|
| IAM Engineer | JML lifecycle, AGDLP groups, OU design, PowerShell bulk operations, LDAPS |
| IT Infrastructure | DC promotion, DNS, DHCP, file server, GPO, AD Sites, FSMO, backup, troubleshooting |
| Cloud Security Engineer | Firewall hardening, PKI/CA, LDAPS, attack surface understanding, certificate management |
| SOC Analyst | Audit policies, Event ID analysis, PowerShell log queries, attack technique detection |
| IT Support | User lifecycle, password resets, account unlocks, drive mapping, domain join |

---

## Tools and Technologies

| Category | Tools |
|----------|-------|
| Operating Systems | Windows Server 2025 (Evaluation), Windows 11 |
| Virtualisation | VMware Workstation - thin-provisioned VMs |
| Firewall / Router | OPNsense (FreeBSD-based) |
| AD Tools | ADUC, GPMC, AD Sites and Services, DNS Manager, DHCP Console |
| Security Tools | Certification Authority (AD CS), ldp.exe, auditpol, Event Viewer |
| Diagnostic Tools | repadmin, dcdiag, netdom, nltest, w32tm |
| Scripting | PowerShell 5.1 - ActiveDirectory module |
| Monitoring | Windows Security Event Log, Get-WinEvent, auditpol |

---

## Repository Structure

```
active-directory-homelab/
├── screenshots/
|   ├── A-AD-Structure/
|   ├── B-MultiSite-Replication/
|   ├── C-Group-Policy-Security/
|   ├── D-FileServer-Client/
|   ├── E-PowerShell-Automation/
|   ├── F-Monitoring-SOC/
|   ├── G-PKI-LDAPS/
|   └── H-Backup-DHCP/
├── notes/
|   └── AD_Theory_Notes.pdf
└── README.md
```

---

## About

Built by **Praveenkumar Saminathan**

MSc Geoinformatics Engineering - Politecnico di Milano, Milan, Italy (Graduating July 2026)

3+ years IT support experience including Tata Consultancy Services and freelance IT support in Milan.

**Certifications:** AWS Cloud Practitioner | AZ-900 (Microsoft Azure Fundamentals) | SC-900 (Microsoft Security, Compliance and Identity Fundamentals)

**Target Roles:** IAM Engineer | IT Infrastructure | IT Security | Cloud Security | SOC Analyst | IT Support | Cloud Support

- Portfolio: [praveenkumar-saminathan](https://orange3456.github.io/praveenkumar-saminathan.github.io/)
- LinkedIn: [praveen-kumar-s-993902228](https://www.linkedin.com/in/praveen-kumar-s-993902228)
- GitHub: [Orange3456](https://github.com/Orange3456)

---

> This lab was built entirely hands-on - every VM created from scratch, every command typed, every error troubleshot and fixed. The process of building it is as valuable as the result.
