# Configuring On-premises  Active Directory within Azure VM's
<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>
<ol type="1">
  <li>Set up Resources in Azure</li>
  <li>Check Connectivity Between the Client and the Domain Controller</li>
  <li>Install Active Directory</li>
  <li>Create an Admin and Normal User Account in Active Directory</li>
  <li>Connect client-1 to the Domain</li>
  </ol>

<h2>Deployment and Configuration Steps</h2>

<p>
  <dl>
    <dt>1.Setup Resources in Azure</dt>
      <dd>
        <li>Create the Domain Controller VM (Windows Server 2022) named “DC-1”</li>
        <li>Set Domain Controller’s NIC Private IP address as static (DC-1>Networking>Network Interface>IP Configurations)</li>
        <li>Create the Client VM (Windows 10) named “Client-1”. Use the same Resource Group from step 1 and make sure it’s on the same virtual network as DC-1</li>
  </dl>
</p>
<br />

<p>
<img src="https://i.imgur.com/wAXDXOk.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  <dl>
    <dt>2.Check Connectivity between the client and Domain Controller</dt>
        <dd>
        <li>Log in to Client-1 with Remote Desktop and ping DC-1’s private IP address (found in Azure or with ipconfig when logged into DC-1) with ping -t <ip address> (perpetual ping in command prompt)</li>
        </dd>
        </dl>
          </p>
          
          
<img src="https://i.imgur.com/V2V7QAZ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<p>
          <ul>
        <li>Log in to the Domain Controller and enable ICMPv4 in on the local windows Firewall</li>
        <li>Check back at Client-1 to see the ping succeed</li>
          </ul>     
</p>
<br />

<p>
<img src="https://i.imgur.com/6COsheE.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  <dl>
<dt>Install Active Directory</dt>
    <dd>
    <li>Log in to DC-1 and install Active Directory Domain Services</li>
    <li>Promote as a DC: Setup a new forest as mydomain.com (the name can be whatever you want)</li>
    <li>Restart and then log back into DC-1 as user: mydomain.com\labuser</li>
    </dd>
  </dl>
</p>
<br />

<p>
<img src="https://i.imgur.com/z8YAUNa.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  <dl>
    <dt>3.Create an Admin and Normal User Account in Active Directory</dt>
      <dd>
        <li>In Active Directory Users and Computers (search for it in the windows search bar), create an Organizational Unit (OU) called “_EMPLOYEES”</li>
        <li>Create a new OU named “_ADMINS”</li>
        <li>Create a new employee. For example “Bart Simpson” (same password) with the username of “bart_admin”</li>
        <li>Add bart_admin to the “Domain Admins” Security Group</li>
        <li>Log out/close the Remote Desktop connection to DC-1 and log back in as “mydomain.com\bart_admin”</li>
        <li>bart_admin is your admin account so log in with this now instead of labuser</li>
      </dd>
  </dl>
</p>

<p>
<img src="https://i.imgur.com/t2C86P6.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  <dl>
    <dt>Connect client-1 to the domain</dt>
    <dd>
    <li>n azure, we now need to set client-1’s DNS settings to DC-1’s private ip address (virtual machines>client-1>Networking>DNS server)</li>
    <li>Save settings and restart client-1 from azure</li>
    <li>Log back in to client-1 with remote desktop</li>
    <li>Also log in to DC-1 (if you aren’t already) and check to make sure client-1 shows up in “Active Directory Users and Computers” (ADUC) inside the “computers” container in the root of the domain</li>
    <li>Make a new OU named “_CLIENTS” and drag Client-1 into it</li>
    </dd>
    </dl>

</p>

<p>
<img src="https://i.imgur.com/BNKFhSw.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
<dl> 
  <dt>Set up Remote Desktop for non-admin users on Client-1</dt>
    <dd>
    <li>On client-1,logged in as bart_admin, open “system properties” (system>about>remote desktop)</li>
    <li>Allow “Domain Users” to access the remote desktop</li>
    <li>Normally you’d want to do this in “Group policy” so you can change many systems at once, but since we just need to change one system then we can do it this way</li>
    </dd>
</dl>
</p>

<p>
<img src="https://i.imgur.com/JyEgtUa.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  <ul>
<li>Now we can create (non-admin) users and log in to client-1 with one of them to test it</li>
<li>On DC-1 (as bart_admin) open Powershell_ise as administrator</li>
<li>Create a new file and paste this script (https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)</li>
<li>This script will just make a lot of users with random names (I didn’t write the script)</li>
<li>You can change the number of users if you want so it won’t make 10,000 users</li>
<li>Choose one of the accounts to test and you should be able to log in (with the password noted in the script)</li>
  </ul>
<p>

<br />
