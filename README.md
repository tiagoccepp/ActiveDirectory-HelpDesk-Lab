# Active Directory Help Desk Lab — Built From Scratch, Documented Step by Step

A hands-on AD lab for a fictional 2-department, 6-user company (`lab.local`):
domain + DNS deployment, CSV-driven PowerShell onboarding automation, security
groups, OU structure, NTFS/share permissions, and GPO hardening — mapped to
CIS Controls v8, with every step screenshotted.

![PowerShell user creation script running](screenshots/07-powershell-user-creation-script.png)

![Active Directory](https://img.shields.io/badge/Active_Directory-lab.local-0078D4?logo=windows&logoColor=white)
![PowerShell](https://img.shields.io/badge/PowerShell-automation-5391FE?logo=powershell&logoColor=white)
![GPO](https://img.shields.io/badge/GPO-hardening-E5A13D)
![CIS](https://img.shields.io/badge/CIS_Controls-v8_mapped-34C77B)

**Host:** Windows Server 2025 (Eval) on VirtualBox · **Net:** NAT + isolated
internal segment `adlab` (192.168.50.0/24) · **DC:** 192.168.50.10, DNS 127.0.0.1

## What it covers

- AD DS deployment + promotion to DC (`lab.local`), DNS forward/reverse zones
- PowerShell user onboarding from CSV (temp password once, force change at
  first logon, per-user error handling) — [`scripts/Create-Users.ps1`](scripts/Create-Users.ps1)
- OUs (IT, RRHH), security groups (`IT-Support`, `RRHH-Team`)
- Defense in depth: NTFS Modify + SMB Full Control on `RRHH-Docs`
  (effective = most restrictive = NTFS)
- GPO disabling USB **storage** on RRHH OU via `UsbStor` driver
  (Start=4) — keyboards/mice unaffected
- Static IP without gateway via `New-NetIPAddress` (`sconfig` can't do blank gateway)

## Lab walkthrough (14 screenshots)

Full narrated walkthrough: [`screenshots/README.md`](screenshots/README.md).
Network topology: [`network-diagrams/`](network-diagrams/).

| # | Step | # | Step |
|---|---|---|---|
| 01 | Static IP via PowerShell | 08 | Script execution policy fix |
| 02 | Installing AD DS role | 09 | Security groups |
| 03 | Promoted to DC (`lab.local`) | 10 | Users added to groups |
| 04–05 | IT + RRHH OUs | 11 | DNS config |
| 06 | Users CSV (automation input) | 12–13 | NTFS + SMB share |
| 07 | PowerShell batch creation | 14 | GPO USB-storage block |

## Reproduce it

1. VirtualBox VM: Windows Server 2025 (Eval), adapters NAT + Internal (`adlab`).
2. Static IP: `New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 192.168.50.10 -PrefixLength 24`
   + `Set-DnsClientServerAddress ... -ServerAddresses 127.0.0.1`.
3. `Install-WindowsFeature AD-Domain-Services -IncludeManagementTools`
   → `Install-ADDSForest -DomainName "lab.local"`.
4. Run [`scripts/Create-Users.ps1`](scripts/Create-Users.ps1) with
   [`users.csv`](scripts/users.csv) (6 users, 2 departments).
5. Groups, permissions, GPO as shown in the screenshots.

## Security notes (CIS v8)

- **CIS 4 (Secure Config):** USB-storage GPO on RRHH OU.
- **CIS 6 (Access Control):** two-layer share permissions, ceiling enforced at NTFS.

Roadmap: domain-join a Windows client → simulate help-desk tickets
(lockout, transfer, offboarding) → track in Jira. Core lab is complete.

## Structure

```
├── screenshots/         <- 14 step screenshots + narrated README.md
├── network-diagrams/    <- Topology + adapter visuals
└── scripts/             <- Create-Users.ps1 + users.csv (6 users)
```

## License note

No `LICENSE` file in repo yet — recommend adding one (e.g. MIT) so the
automation script is reusable.

---

**Tiago Colo** · colotiago8@gmail.com · [LinkedIn](https://www.linkedin.com/in/tiago-colo-640057402/)
