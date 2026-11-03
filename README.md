# Active Directory Deployment and Administration in Azure

> **Portfolio lab:** Personal training environment; not a production system.

## Overview

A two-VM lab modeling an on-premises-style Windows domain in Microsoft Azure. The original project documents DC-1 (Windows Server 2022) and Client-1 (Windows 10), AD DS deployment, domain join, users and organizational units, Group Policy, PowerShell user creation, and account lockout exercises.

## Tools and technologies

Windows Server 2022, Windows 10, Microsoft Azure, Active Directory Domain Services, Group Policy, PowerShell, RDP, Event Viewer

## What I did

1. Provision Azure virtual machines and a virtual network; assign the domain controller a stable private address.
2. Install and configure AD DS; join the Windows client to the domain.
3. Create and manage organizational units, users, and groups; test Group Policy.
4. Use PowerShell for lab account creation and simulate account lockouts; inspect results.

## Selected evidence

### Domain controller and client

![Domain controller and client — 1](./dc-1.png)
![Domain controller and client — 2](./client-1.png)

### Group Policy and lockout

![Group Policy and lockout — 1](./gpo.png)
![Group Policy and lockout — 2](./lockout.png)

### User administration

![User administration — 1](./ou.png)
![User administration — 2](./reset.png)

## What I learned

This lab strengthened my understanding of how identity, DNS, policies, and endpoint configuration interact. Lockout exercises provided a practical introduction to access troubleshooting and security event investigation.

## Related work

[Back to portfolio](https://github.com/lukasparmelee/LukasParmelee)

## Security and scope

Screenshots and instructions describe a controlled learning lab. Do not reuse demonstration credentials, expose live secrets, or reproduce permissive firewall settings in production. Verify screenshots and repository history contain no active credentials before publishing.
