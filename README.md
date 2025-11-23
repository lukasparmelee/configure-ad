<p align="center">
  <img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of an on-premises-style Active Directory environment deployed within Microsoft Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines / Compute)
- Remote Desktop (RDP)
- Active Directory Domain Services (AD DS)
- PowerShell

<h2>Operating Systems Used</h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Deploy and configure the Domain Controller (DC-1)
- Deploy and configure the Client (Client-1)
- Verify connectivity and DNS configuration
- Prepare for future Active Directory labs

<h2>Deployment and Configuration Steps</h2>

<h3>Setup Domain Controller in Azure</h3>

<p>
  <img src="./dc-1.png" height="80%" width="80%" alt="Azure Resource Group Creation"/>
</p>

<p>
<strong>1.</strong> Create a <strong>Resource Group</strong> in the Azure Portal.<br />
<strong>2.</strong> Create a <strong>Virtual Network</strong> and <strong>Subnet</strong>.<br />
<strong>3.</strong> Create the <strong>Domain Controller VM</strong> using the following details:
<ul>
  <li><strong>Name:</strong> DC-1</li>
  <li><strong>Operating System:</strong> Windows Server 2022</li>
  <li><strong>Username:</strong> labuser</li>
  <li><strong>Password:</strong> Cyberlab123!</li>
</ul>
<strong>4.</strong> After creation, set the Domain Controller’s <strong>NIC Private IP address to Static</strong>.<br />
<strong>5.</strong> Log in to <strong>DC-1</strong> via Remote Desktop.<br />
<strong>6.</strong> Disable the <strong>Windows Firewall</strong> (for testing connectivity).<br />
</p>

<p align="center">
  <img src="./firewall.png" height="80%" width="80%" alt="Disabling Windows Firewall on DC-1"/>
</p>

<br />

<h3>Setup Client-1 in Azure</h3>

<p>
  <img src="./client-1.png" height="80%" width="80%" alt="Azure Client VM Creation"/>
</p>

<p>
<strong>1.</strong> Create the <strong>Client VM</strong> using the following details:
<ul>
  <li><strong>Name:</strong> Client-1</li>
  <li><strong>Operating System:</strong> Windows 10 (21H2)</li>
  <li><strong>Username:</strong> labuser</li>
  <li><strong>Password:</strong> Cyberlab123!</li>
</ul>
<strong>2.</strong> Attach Client-1 to the <strong>same region and Virtual Network</strong> as DC-1.<br />
<strong>3.</strong> After creation, set <strong>Client-1’s DNS settings</strong> to DC-1’s Private IP address.<br />
<strong>4.</strong> From the Azure Portal, <strong>restart Client-1</strong>.<br />
<strong>5.</strong> Log in to Client-1 using Remote Desktop.<br />
<strong>6.</strong> Open Command Prompt and <strong>ping DC-1’s private IP address</strong>.<br />
</p>

<p align="center">
  <img src="./ping.png" height="80%" width="80%" alt="Ping Test Between Client-1 and DC-1"/>
</p>

<p>
<strong>7.</strong> Confirm that the ping succeeds, verifying network connectivity between both VMs.<br />
<strong>8.</strong> From Client-1, open <strong>PowerShell</strong> and run:
<pre><code>ipconfig /all</code></pre>
<strong>9.</strong> Verify that the <strong>DNS server</strong> is set to DC-1’s private IP address.
</p>

<p align="center">
  <img src="./ipconfig.png" height="80%" width="80%" alt="ipconfig /all output showing DC-1 as DNS"/>
</p>

Part 1 — Deploy & Configure Active Directory

1. Install Active Directory Domain Services (AD DS)
	1.	Log into DC-1.
	2.	Open Server Manager → Add Roles and Features.
	3.	Install Active Directory Domain Services.
	4.	Promote DC-1 to a Domain Controller.
	5.	Create a new forest: mydomain.com
	6. Restart and log back in as mydomain.com\labuser
![Install ADDS](./adds.png)
![Promote DC](./dc.png)

2. Create a Domain Admin User
	1.	Open Active Directory Users and Computers (ADUC).
	2.	Create:
	    _EMPLOYEES OU
	    _ADMINS OU
	3.	Create a user:
    Name: Jane Doe
	Username: jane_admin
	Password: Cyberlab123!
	4.	Add jane_admin to: Domain Admins
   	5.	Log out and log in as: mydomain.com\jane_admin
  
	![Create OUs](./ou.png)
	![Create Admin User](./admin.png)

---

4. Join Client-1 to the Domain
	1.	DNS already set in Azure to point Client-1 to DC-1.
	2.	Restart Client-1.
	3.	Log in as local admin: labuser
 	4.	Join domain: mydomain.com
  	5.	Restart Client-1.
	6.	In ADUC, verify Client-1 appears.
	7.	Create OU: _CLIENTS
	8.	Move Client-1 into it.

![Join Domain](./domain.png)
![Client OU](./clients.png)

---

Part 2 — Enable RDP + Bulk User Creation

1. Enable Remote Desktop for Domain Users on Client-1
		1.	Log into Client-1 as: mydomain.com\jane_admin
    	2.	Open: System Properties → Remote Desktop
        3.	Add: Domain Users
![Enable RDP](./rdp.png)

2. Bulk Create Users with PowerShell
	1.	Log into DC-1 as jane_admin.
	2.	Open PowerShell ISE as Administrator.
	3.	Paste the bulk user creation script.
	4.	Run it and observe user creation.

![PowerShell ISE](./powershell.png)

3. Verify Accounts in ADUC

Users should appear under the _EMPLOYEES OU.

![Employees OU](./employees.png)

4. Test Login With One of the New Accounts

Use RDP to log into Client-1 with a newly created user account.
Login: mydomain.com\daxer.rop
Password: Password1

![Login Test](./login.png)

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Active Directory Lab – Account Lockouts & Security Operations</title>
<style>
    body {
        font-family: Arial, Helvetica, sans-serif;
        margin: 0;
        padding: 0;
        line-height: 1.6;
        background: #f7f7f7;
    }

    header {
        background: #1f2937;
        color: white;
        padding: 20px;
        text-align: center;
    }

    .container {
        width: 90%;
        max-width: 900px;
        margin: auto;
        background: white;
        padding: 25px;
        margin-top: 25px;
        margin-bottom: 40px;
        border-radius: 8px;
        box-shadow: 0 2px 6px rgba(0,0,0,0.15);
    }

    h2, h3 {
        color: #1f2937;
    }

    .screenshot {
        margin: 15px 0;
        background: #eee;
        padding: 10px;
        border-left: 4px solid #4b5563;
        font-size: 14px;
        font-family: monospace;
    }

    .note {
        background: #e0f2fe;
        padding: 12px;
        border-left: 4px solid #0284c7;
        margin: 15px 0;
        border-radius: 5px;
    }

    .warning {
        background: #fee2e2;
        padding: 12px;
        border-left: 4px solid #dc2626;
        margin: 15px 0;
        border-radius: 5px;
    }
</style>
</head>

<body>

<header>
    <h1>Active Directory Lab — Account Lockouts, Account Control, & Log Analysis</h1>
    <p>Continuation of Azure-hosted AD DS Deployment</p>
</header>

<div class="container">

<h2>Start the Lab</h2>
<p>Turn on <strong>DC-1</strong> and <strong>Client-1</strong> in the Azure Portal.</p>

<div class="screenshot">
    <!-- Insert screenshot -->
    Screenshot: Start VMs (./vms.png)
</div>

<hr>

<h2>Part 1 — Triggering an Account Lockout</h2>

<h3>1. Log into DC-1</h3>
<p>Use your domain admin account to connect to your domain controller.</p>

<div class="screenshot">Screenshot: Login to DC-1</div>

<h3>2. Select a Test User</h3>
<p>Choose any user you previously created using your PowerShell bulk-creation script.</p>

<div class="screenshot">Screenshot: User list in ADUC</div>

<h3>3. Attempt 10 Bad Logins</h3>
<p>On Client-1, attempt to log in with the wrong password 10 times.</p>

<div class="screenshot">Screenshot: Failed logins on Client-1</div>

<hr>

<h2>Part 2 — Configure Account Lockout Policy</h2>

<h3>4. Edit Group Policy</h3>
<p>Navigate to:</p>
<ul>
    <li><strong>Group Policy Management</strong></li>
    <li><strong>Default Domain Policy → Edit</strong></li>
</ul>

<p>Modify the account lockout settings:</p>

<table border="1" cellpadding="8">
<tr><th>Setting</th><th>Value</th></tr>
<tr><td>Account lockout threshold</td><td>5 attempts</td></tr>
<tr><td>Reset counter after</td><td>5 minutes</td></tr>
<tr><td>Account lockout duration</td><td>5 minutes</td></tr>
</table>

<div class="screenshot">Screenshot: GPO settings for account lockout</div>

<div class="note">
Run <strong>gpupdate /force</strong> on both DC-1 and Client-1.
</div>

<h3>5. Attempt 6 Bad Logins</h3>
<p>The account should now be locked after the sixth attempt.</p>

<div class="screenshot">Screenshot: Account locked during login</div>

<hr>

<h2>Part 3 — Unlocking & Resetting the Account</h2>

<h3>6. View the Locked-Out User in ADUC</h3>
<p>The user will show a “locked out” status in its Account tab.</p>

<div class="screenshot">Screenshot: Locked-out user in ADUC</div>

<h3>7. Unlock & Reset Password</h3>
<p>Check “Unlock account” and optionally reset the password.</p>

<div class="screenshot">Screenshot: Unlock account</div>

<h3>8. Successful Login Test</h3>
<p>Attempt to log in again on Client-1 with the correct password.</p>

<div class="screenshot">Screenshot: Successful login</div>

<hr>

<h2>Part 4 — Disabling & Re-Enabling Accounts</h2>

<h3>9. Disable the User</h3>
<p>Right-click the user in ADUC → <strong>Disable Account</strong>.</p>

<div class="screenshot">Screenshot: Disable account</div>

<h3>10. Attempt Login While Disabled</h3>
<p>A disabled account will show an error like:</p>
<blockquote>Your account has been disabled. Please contact your administrator.</blockquote>

<div class="screenshot">Screenshot: Disabled login error</div>

<h3>11. Re-Enable the Account</h3>
<p>Right-click → <strong>Enable Account</strong>.</p>

<div class="screenshot">Screenshot: Enable account</div>

<h3>12. Login Test After Re-Enabling</h3>

<div class="screenshot">Screenshot: Login after enabling</div>

<hr>

<h2>Part 5 — Log Analysis (Security Operations)</h2>

<h3>13. Inspect Logs on DC-1</h3>
<p>Open Event Viewer:</p>

<ul>
    <li><strong>Windows Logs → Security</strong></li>
</ul>

Look for:
<ul>
    <li><strong>4625</strong> — Failed logon attempts</li>
    <li><strong>4740</strong> — Account locked out</li>
</ul>

<div class="screenshot">Screenshot: Security logs on DC-1</div>

<h3>14. Inspect Logs on Client-1</h3>
<p>Open Event Viewer → Security and check authentication attempts.</p>

<div class="screenshot">Screenshot: Client-1 logs</div>

<div class="note">
This is foundational training for SOC (Security Operations Center) roles.
More: <a href="https://joshmadakor.tech/cyber">joshmadakor.tech/cyber</a>
</div>

<hr>

<h2>Finishing the Lab</h2>

<div class="warning">
<strong>Do NOT delete</strong> your VMs.  
Simply STOP them in the Azure Portal to save money.
</div>

<p>This environment is required for upcoming labs.</p>

</div>
</body>
</html>
	
