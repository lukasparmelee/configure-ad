<p align="center">
  <img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of an on-premises-style Active Directory environment deployed within Microsoft Azure Virtual Machines.<br />

<h2>Video Demonstration</h2>

- ### [YouTube: How to Deploy On-premises Active Directory within Azure Compute](https://www.youtube.com)

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
  <img src="./azure-ad/dc-1.png" height="80%" width="80%" alt="Azure Resource Group Creation"/>
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

<br />

<h3>Setup Client-1 in Azure</h3>

<p>
  <img src="./azure-ad/client-1.png" height="80%" width="80%" alt="Azure Client VM Creation"/>
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
  <img src="./azure-ad/ping.png" height="80%" width="80%" alt="Ping Test Between Client-1 and DC-1"/>
</p>

<p>
<strong>7.</strong> Confirm that the ping succeeds, verifying network connectivity between both VMs.<br />
<strong>8.</strong> From Client-1, open <strong>PowerShell</strong> and run:
<pre><code>ipconfig /all</code></pre>
<strong>9.</strong> Verify that the <strong>DNS server</strong> is set to DC-1’s private IP address.
</p>
