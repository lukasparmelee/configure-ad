# Active Directory Deployment & Administration Lab (Azure)

> **Hands-on IT infrastructure lab** · Windows Server 2022 · Windows 10 · Microsoft Azure · AD DS · Group Policy · PowerShell

## Project overview

Built an Active Directory domain environment in Microsoft Azure using a Windows Server 2022 domain controller (`DC-1`) and a Windows 10 workstation (`Client-1`). This walkthrough documents the original setup, domain join, organizational units and administrative accounts, remote access, bulk user provisioning, and account lockout testing.

**Skills demonstrated:** Windows Server administration, identity and access management, DNS configuration, Azure virtual networking, PowerShell automation, Group Policy, and troubleshooting.

**Environment:** A personal training lab that has since been decommissioned; this is not a production deployment.

## Lab architecture

```text
Microsoft Azure virtual network
├── DC-1 — Windows Server 2022
│   ├── Active Directory Domain Services
│   ├── Domain DNS
│   └── Domain administration / Group Policy
└── Client-1 — Windows 10 (21H2)
    ├── DNS configured to use DC-1
    └── Joined to mydomain.com
```

## Walkthrough

The following retains the detailed implementation notes and screenshot references from the original lab documentation. Screenshots are expected to remain in the repository root alongside this README.

### 🖧 Active Directory Deployment, Configuration & Security Operations Lab (Azure)

This tutorial documents the full deployment of an on-premises style Active Directory environment hosted in Microsoft Azure using two virtual machines:
• DC-1 (Windows Server 2022)
• Client-1 (Windows 10)

You will configure AD DS, join a client to the domain, create domain admins, enable RDP, bulk-create users, and simulate account lockouts.

---

## 🧰 Environments and Technologies Used
- Microsoft Azure (Virtual Machines / Networking)
- Remote Desktop Protocol (RDP)
- Active Directory Domain Services (AD DS)
- Group Policy Management
- PowerShell / PowerShell ISE
- Windows Event Viewer

---

## 💻 Operating Systems Used
- Windows Server 2022 (Domain Controller)
- Windows 10 (21H2) (Client Machine)

---

## 🏗️ Part 1 — Deploy & Configure Active Directory

---

### ⚙️ Step 1: Create Domain Controller (DC-1)

Azure Setup

Created a new:
- Resource Group
- Virtual Network + Subnet
- Virtual Machine:
- Name: DC-1
- OS: Windows Server 2022
- Username: labuser
- Password: [REDACTED — retired lab credential]

Set DC-1’s NIC Private IP to Static after deployment.

![Domain Controller VM creation](./dc-1.png)

---

Logged into DC-1 via RDP and disabled Windows Firewall temporarily for connectivity testing.

![Disable Firewall](./firewall.png)

---

### ⚙️ Step 2: Create Client VM (Client-1)

Created:
• VM Name: Client-1
• OS: Windows 10 (21H2)
• Username: labuser
• Password: [REDACTED — retired lab credential]

Attached it to the same region and VNet as DC-1.

After deployment:
• Set Client-1’s DNS server to DC-1’s private IP.
• Restarted the VM.

![Client VM creation](./client-1.png)

---

Verified connectivity:
1. Logged into Client-1
2. Ran: ping

![Ping Test](./ping.png)

3. Ran: ipconfig /all

Confirmed DNS = DC-1’s IP.

![ipconfig output](./ipconfig.png)

---

### ⚙️ Step 3: Install Active Directory Domain Services (AD DS)
1. Logged into DC-1
2. Opened Server Manager → Add Roles and Features
3. Installed Active Directory Domain Services
4. Promoted DC-1 to a Domain Controller
5. Created a NEW FOREST:

Domain: mydomain.com
6. Restarted the server and logged in as:

mydomain.com\labuser

![Install AD DS](./adds.png)
![Promote to Domain Controller](./dc.png)

---

### ⚙️ Step 4: Create OUs and Domain Admin

Opened Active Directory Users and Computers (ADUC)
Created two OUs:
• \_EMPLOYEES
• \_ADMINS

Created a Domain Admin account:

User: Jane Doe
Username: jane\_admin
Password: [REDACTED — retired lab credential]

Added to group:
➡️ Domain Admins

Logged in as mydomain.com\jane\_admin.

![OU Creation](./ou.png)
![Admin user creation](./admin.png)

---

### ⚙️ Step 5: Join Client-1 to the Domain
1. DNS already points to DC-1
2. Restarted Client-1
3. Logged in as local user labuser
4. Joined domain: mydomain.com
5. Restarted Client-1
6. Verified Client-1 appears in ADUC
7. Created OU: \_CLIENTS
8. Moved Client-1 into \_CLIENTS

![Join Domain](./domain.png)
![Client OU](./clients.png)

---

## 🧩 Part 2 — RDP Permissions & Bulk User Creation

---

### 🔐 Step 1: Enable RDP for Domain Users

Logged into Client-1 as mydomain.com\jane\_admin:
• Opened System Properties → Remote Desktop
• Added: Domain Users

---

### 👥 Step 2: Bulk Create Users With PowerShell
1. Logged into DC-1
2. Opened PowerShell ISE as Administrator
3. Pasted bulk user creation script
4. Executed script—hundreds of accounts created under \_EMPLOYEES

![PowerShell ISE](./powershell.png)

Verified in ADUC:

![Employees OU](./employees.png)

---

🔑 Step 3: Test Login With New Account

Tested RDP login to Client-1:

User: mydomain.com\daxer.rop
Password: Password1

![Login Test](./login.png)

---

## 🔐 Part 3 — Account Lockouts, GPO, Security Logs, and Account Management

This section covers cybersecurity-relevant Active Directory operations, including lockout policies, and account disabling.

---

🚀 Step 1: Ensure Both VMs Are Running

![Azure VMs Running](./vms.png)

Logged into DC-1:

![Login DC-1](./dc1.png)

---

🚀 Step 2: Select a User for Lockout Testing

Example user:

Username: gok.paq
Password: Password1

---

🛠️ Part 4 — Configure Group Policy Lockout Threshold

Step 1: Open Group Policy Management

Path:
Group Policy Management → Default Domain Policy → Edit

![Group Policy](./gpo.png)

---

Step 2: Configure Account Lockout Settings

Path:
Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Account Lockout Policy

Configured:
• Lockout Threshold: 5 attempts
• Reset Counter: 10 minutes
• Lockout Duration: 30 minutes

![Lockout Policy](./lockout.png)

---

Step 3: Force Group Policy Update

Run on both DC-1 and Client-1:

gpupdate /force

![GPUpdate](./gpupdate.png)

---

Step 4: Trigger Account Lockout

Attempted 6 incorrect logins:

![Locked Out](./locked.png)

---

Step 5: Confirm Lockout in ADUC

User shows Account is locked out.

![Verify Lockout](./verify.png)

---

Step 6: Unlock + Reset Password
• Unchecked Unlock account
• Reset password
• Tested login again

![Reset Account](./reset.png)

---

🛑 Part 5 — Enable & Disable Accounts

Step 1: Disable the User

Disabled account: gok.paq

![Disable Account](./disable.png)

---

Step 2: Test Login While Disabled

User receives “Account Disabled” message.

![Disabled Login](./unable.png)

---

Step 3: Re-Enable Account

Right-click → Enable Account

![Enabled Account](./enabled.png)

---
## Practical takeaways

- A domain-joined workstation relies on correct DNS settings to locate the domain controller.
- Organizational units and security groups support structured administration and access control.
- PowerShell can streamline repetitive user-provisioning tasks.
- Account lockout testing connects Group Policy settings to observable end-user behavior.

## Security and scope

This lab used isolated training VMs that have since been deleted. Example passwords have been redacted from this version; do not reuse demonstration credentials. Temporarily disabling the Windows Firewall was part of the original connectivity testing, **not a recommended production configuration**. In production, use narrowly scoped firewall rules and least-privilege administrative access.

[← Back to GitHub profile](https://github.com/lukasparmelee)
