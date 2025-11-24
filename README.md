<p align="center">
  <img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

🖧 Active Directory Deployment, Configuration & Security Operations Lab (Azure)

This tutorial documents the full deployment of an on-premises style Active Directory environment hosted in Microsoft Azure using two virtual machines:
	•	DC-1 (Windows Server 2022)
	•	Client-1 (Windows 10)

You will configure AD DS, join a client to the domain, create domain admins, enable RDP, bulk-create users, and simulate account lockouts.

⸻

🧰 Environments and Technologies Used
	•	Microsoft Azure (Virtual Machines / Networking)
	•	Remote Desktop Protocol (RDP)
	•	Active Directory Domain Services (AD DS)
	•	Group Policy Management
	•	PowerShell / PowerShell ISE
	•	Windows Event Viewer

⸻

💻 Operating Systems Used
	•	Windows Server 2022 (Domain Controller)
	•	Windows 10 (21H2) (Client Machine)

⸻

🏗️ Part 1 — Deploy & Configure Active Directory

⸻

⚙️ Step 1: Create Domain Controller (DC-1)

Azure Setup

Created a new:
	•	Resource Group
	•	Virtual Network + Subnet
	•	Virtual Machine:
	•	Name: DC-1
	•	OS: Windows Server 2022
	•	Username: labuser
	•	Password: Cyberlab123!

Set DC-1’s NIC Private IP to Static after deployment.

<p align="center">
  <img src="./dc-1.png" width="80%" alt="Domain Controller VM creation"/>
</p>

---

Logged into DC-1 via RDP and disabled Windows Firewall temporarily for connectivity testing.

<p align="center">
  <img src="./firewall.png" width="80%" alt="Disable Firewall"/>
</p>

---

⚙️ Step 2: Create Client VM (Client-1)

Created:
	•	VM Name: Client-1
	•	OS: Windows 10 (21H2)
	•	Username: labuser
	•	Password: Cyberlab123!

Attached it to the same region and VNet as DC-1.

After deployment:
	•	Set Client-1’s DNS server to DC-1’s private IP.
	•	Restarted the VM.

<p align="center">
  <img src="./client-1.png" width="80%" alt="Client VM creation"/>
</p>

---

Verified connectivity:
	1.	Logged into Client-1
	2.	Ran: ping <DC-1-private-IP>

<p align="center">
  <img src="./ping.png" width="80%" alt="Ping Test"/>
</p>

3.	Ran: ipconfig /all

Confirmed DNS = DC-1’s IP.

<p align="center">
  <img src="./ipconfig.png" width="80%" alt="ipconfig output"/>
</p>

---

⚙️ Step 3: Install Active Directory Domain Services (AD DS)
	1.	Logged into DC-1
	2.	Opened Server Manager → Add Roles and Features
	3.	Installed Active Directory Domain Services
	4.	Promoted DC-1 to a Domain Controller
	5.	Created a NEW FOREST:

Domain: mydomain.com
	6.	Restarted the server and logged in as:

mydomain.com\labuser

<p align="center">
  <img src="./adds.png" width="80%" alt="Install AD DS"/>
  <img src="./dc.png" width="80%" alt="Promote to Domain Controller"/>
</p>

---

⚙️ Step 4: Create OUs and Domain Admin

Opened Active Directory Users and Computers (ADUC)
Created two OUs:
	•	_EMPLOYEES
	•	_ADMINS

Created a Domain Admin account:

User: Jane Doe
Username: jane_admin
Password: Cyberlab123!

Added to group:
➡️ Domain Admins

Logged in as mydomain.com\jane_admin.

<p align="center">
  <img src="./ou.png" width="80%" alt="OU Creation"/>
  <img src="./admin.png" width="80%" alt="Admin user creation"/>
</p>

---

⚙️ Step 5: Join Client-1 to the Domain
	1.	DNS already points to DC-1
	2.	Restarted Client-1
	3.	Logged in as local user labuser
	4.	Joined domain: mydomain.com
	5.	Restarted Client-1
	6.	Verified Client-1 appears in ADUC
	7.	Created OU: _CLIENTS
	8.	Moved Client-1 into _CLIENTS

<p align="center">
  <img src="./domain.png" width="80%" alt="Join Domain"/>
  <img src="./clients.png" width="80%" alt="Client OU"/>
</p>

---

🧩 Part 2 — RDP Permissions & Bulk User Creation

---

🔐 Step 1: Enable RDP for Domain Users

Logged into Client-1 as mydomain.com\jane_admin:
	•	Opened System Properties → Remote Desktop
	•	Added: Domain Users

---

👥 Step 2: Bulk Create Users With PowerShell
	1.	Logged into DC-1
	2.	Opened PowerShell ISE as Administrator
	3.	Pasted bulk user creation script
	4.	Executed script—hundreds of accounts created under _EMPLOYEES

<p align="center">
  <img src="./powershell.png" width="80%" alt="PowerShell ISE"/>
</p>

Verified in ADUC:

<p align="center">
  <img src="./employees.png" width="80%" alt="Employees OU"/>
</p>

---

🔑 Step 3: Test Login With New Account

Tested RDP login to Client-1:

User: mydomain.com\daxer.rop
Password: Password1

<p align="center">
  <img src="./login.png" width="80%" alt="Login Test"/>
</p>

---

🔐 Part 3 — Account Lockouts, GPO, Security Logs, and Account Management

This section covers cybersecurity-relevant Active Directory operations, including lockout policies, and account disabling.

---

🚀 Step 1: Ensure Both VMs Are Running

<p align="center">
  <img src="./vms.png" width="80%" alt="Azure VMs Running"/>
</p>


Logged into DC-1:

<p align="center">
  <img src="./dc1.png" width="80%" alt="Login DC-1"/>
</p>

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

<p align="center">
  <img src="./gpo.png" width="80%" alt="Group Policy"/>
</p>

---

Step 2: Configure Account Lockout Settings

Path:
Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Account Lockout Policy

Configured:
	•	Lockout Threshold: 5 attempts
	•	Reset Counter: 10 minutes
	•	Lockout Duration: 30 minutes

<p align="center">
  <img src="./lockout.png" width="80%" alt="Lockout Policy"/>
</p>

---

Step 3: Force Group Policy Update

Run on both DC-1 and Client-1:

gpupdate /force

<p align="center">
  <img src="./gpupdate.png" width="80%" alt="GPUpdate"/>
</p>

---

Step 4: Trigger Account Lockout

Attempted 6 incorrect logins:

<p align="center">
  <img src="./locked.png" width="80%" alt="Locked Out"/>
</p>


---

Step 5: Confirm Lockout in ADUC

User shows Account is locked out.

<p align="center">
  <img src="./verify.png" width="80%" alt="Verify Lockout"/>
</p>

---

Step 6: Unlock + Reset Password
	•	Unchecked Unlock account
	•	Reset password
	•	Tested login again

<p align="center">
  <img src="./reset.png" width="80%" alt="Reset Account"/>
</p>


---

🛑 Part 5 — Enable & Disable Accounts

Step 1: Disable the User

Disabled account: gok.paq

<p align="center">
  <img src="./disable.png" width="80%" alt="Disable Account"/>
</p>

---

Step 2: Test Login While Disabled

User receives “Account Disabled” message.

<p align="center">
  <img src="./unable.png" width="80%" alt="Disabled Login"/>
</p>

---

Step 3: Re-Enable Account

Right-click → Enable Account

<p align="center">
  <img src="./enabled.png" width="80%" alt="Enabled Account"/>
</p>

---

🧠 Skills Demonstrated
	•	Azure VM provisioning
	•	Active Directory installation & forest creation
	•	DNS configuration & domain joining
	•	Group Policy configuration
	•	RDP security configuration
	•	Bulk AD user creation (PowerShell)
	•	AD account lifecycle management
	•	Account lockout simulation
