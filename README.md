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

<h2>Tutorial</h2>

**Setting Up a Domain Controller and Client in Azure**
---

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
- Log in to DC-1 via Remote Desktop.
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

**Deploying Active Directory**
---
🔷 **Install Active Directory on DC-1**  
- Log in to **DC-1 via RDP**  
- Open **Server Manager**  
- Navigate to **Add Roles and Features**  
- In **Server Selection**, ensure dc-1 is selected  
- Under **Server Roles**, select **Active Directory Domain Services**  
- Confirm by clicking **Add Features**  
- Click **Install** and wait for the process to complete  

🔷 **Configure Active Directory to become a domain controller**  
- Once Active Directory is installed, click **Promote this server to a domain controller**  
- Select **Add a new forest**  
- Root domain name: **mydomain.com**  
- Create a username and password  
- De-select **Create DNS delegation**  
- Click **Install** (Note: The server will restart)  
- After the restart, choose to log in as a domain user using:  
  `mydomain.com\labuser`  

🔷 **Create Organizational Units**  
- Open **Active Directory Users and Computers (ADUC)**  
- Navigate to **mydomain.com**  
- Right-click on **mydomain.com** > **New** > **Organizational Unit**  
- Create the following Organizational Units (OUs):  
  - `_EMPLOYEES`  
  - `_ADMINS`  
  - `_CLIENTS`  

🔷 **Create a Domain Admin user**  
- In **_ADMINS**, right-click > **New** > **User**  
- Create user: **Jane Doe** with the username: **jane_admin**  
- To make Jane Doe an admin:  
  - Right-click **Jane Doe** > **Properties**  
  - Go to the **Member Of** tab > **Add** > Enter: **Domain Admins**  
  - Click **Apply**  

🔷 **Log out and log back in as Jane Doe**  
- Log out of **DC-1**  
- Log back in to `mydomain.com\jane_admin`  
- Use **jane_admin** as the admin account from now on  

🔷 **Join Client-1 to your domain (mydomain.com)**  
- Log in to **Client-1** as the original local admin (**labuser**) via RDP  
- Open **System** > **Rename this PC (advanced)**  
- Click **Computer Name Change**  
- Under **Member of**, enter: **mydomain.com**  
- Click **OK** and restart **Client-1**  
- Log in to **DC-1** and verify that **Client-1** appears in **Active Directory Users and Computers** under **mydomain.com > Computers**  
- Move **Client-1** into the **_CLIENTS** Organizational Unit  

🔷 **Setup Remote Desktop for non-administrative users on Client-1**  
- Log in to **Client-1** as `mydomain.com\jane_admin` via RDP  
- Open **System** > **Remote Desktop**  
- Click **Select users that can remotely access this PC**  
- Add **Domain Users** > Click **OK**  

🔷 **Create additional users and log in to Client-1**  
- Log in to **DC-1** as `jane_admin` via RDP  
- Open **PowerShell_ise as an Administrator**  
- Create a new file and paste the following [script](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)  
- Run the [script](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1) to create users in the _EMPLOYEES OU  
Each user will have the default password: Password1 (this can be changed)  
- Use RDP to log in to Client-1 with one of the generated user accounts and ensure that log in is successful


