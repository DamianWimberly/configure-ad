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

- Step 1
- Step 2
- Step 3
- Step 4

<h2>Deployment and Configuration Steps</h2>

**Setting Up a Domain Controller and Client in Azure**

🔷 **Create a Resource Group**
- Ensure the virtual network and virtual machines are in the same region as the resource group.

🔷 **Create a Virtual Network and Subnet**
- Attach this to the resource group for proper networking.

🔷 **Create the Domain Controller (DC-1)**
- Image: Windows Server 2022 (at least 2 vCPUs)
- Name: DC-1
- Username: labuser
- Password: Cyberlab123!
- Virtual Network: Select the network created earlier, leave subnet as default.

🔷 **Set DC-1’s Private IP Address to Static**
- This ensures the IP address remains fixed, which is necessary for DNS.
  - Azure Portal > Virtual Machines > DC-1 > Networking > IP Configuration > Set Private IP to Static > Save.

🔷 **Disable the Windows Firewall on DC-1**
- Connect via Remote Desktop.
- Open `wf.msc` from the Run command.
- Go to **Windows Defender Firewall Properties**.
- Set the Firewall state to **Off** for Domain, Private, and Public Profiles.
- Apply the changes.

🔷 **Create the Client-1 VM**
- Image: Windows 10 Pro (at least 2 vCPUs)
- Name: Client-1
- Username: labuser
- Password: Cyberlab123!
- Virtual Network: Select the same network used for DC-1, leave subnet as default.

🔷 **Set Client-1 to Use DC-1 as DNS**
- Update Client-1’s DNS settings to use DC-1’s private IP.
  - Azure Portal > Virtual Machines > Client-1 > Networking > DNS Servers > Set to Custom and enter DC-1’s static private IP > Save.

🔷 **Restart Client-1**
- This applies the new DNS settings.
  - Virtual Machines > Client-1 > Restart.

🔷 **Ping DC-1 from Client-1**
- Open **Command Prompt** or **PowerShell** on Client-1.  
- Run the following command to test connectivity:  
  `ping <DC-1-private-IP-address>`





<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />
