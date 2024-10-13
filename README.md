<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This lab demonstrates the process of setting up and configuring a Domain Controller and Client VM in Azure, integrating them into a functional Active Directory environment. The goal is to establish a domain, join client machines to it, and enforce security policies such as account lockout thresholds. Key tasks include configuring DNS settings, ensuring domain communication between the machines, creating user accounts and Organizational Units (OUs), and managing users' account security by locking and unlocking accounts based on incorrect login attempts. Finally, we will review event logs to observe security-related activities.<br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell
  
<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 

<h2>High-Level Deployment and Configuration Steps</h2>

- Configure Azure Virtual Machines
- Install Active Directory
- Join Client to Domain
- Set DNS Settings
- Create Organizational Units
- Configure Group Policy
- Test Account Lockouts
- Manage User Accounts
- Observe Security Logs

<h2>Tutorial:</h2>

 ***Setting Up a Domain Controller and Client in Azure***
---
*[⚠️ Images include further notes and direction]*


🔷***Create Resource Group and Virtual Network***  
*Ensure proper networking by setting up the virtual network within the same region as the resource group.*

- Create a **Resource Group** in the desired region.
    - **Name**: Active-Directory-Lab 
    - **Region**: (US) East US 2 
- Set up a **Virtual Network**
    - **Name**: Active-Directory-VNet 
    - Attach the **Virtual Network** to the **Resource Group** previously created.
    -  **Region**: (US) East US 2 
<table>
  <tr>
    <td><img width="200" height="150" alt="create-a-resource-group" src="https://github.com/user-attachments/assets/2647a5f2-620e-4388-aa5d-2001ffd95e1c">
</td>
    <td><img width="200" height="150" alt="create-a-virtual-network" src="https://github.com/user-attachments/assets/201775ec-ba42-451b-9fdb-0a8a4bf911ed">
</td>
  <tr>
    <td>Step 1</td>
    <td>Step 2</td>
  </tr>
</table>


🔷***Create the Domain Controller (DC-1)***  
*Create a virtual machine to act as the domain controller. Ensure the virtual machine uses the previously created resource group and virtual network.*
- **Resource Group**: Active-Directory-Lab
- **Name**: DC-1
- **Region**: (US) East US 2
- Image: Windows Server 2022 (at least 2 vCPUs)
- Username: labuser, Password: Cyberlab123!
- **Virtual Network**: Active-Directory-VNet; *leave subnet as default*.

<table>
  <tr>
    <td><img width="200" height="150" alt="create-a-vm-1" src="https://github.com/user-attachments/assets/3ecce515-9fee-409d-927d-5b20b4e65397"></td>
    <td><img width="200" height="150" alt="create-a-vm-2" src="https://github.com/user-attachments/assets/86433202-a1c5-4063-a6c1-89c58b38b0dd"></td>
    <td><img width="200" height="150" alt="create-a-vm-3" src="https://github.com/user-attachments/assets/56231de6-f754-464d-bf9c-f217cfdda215"></td>
    <td><img width="200" height="150" alt="create-a-vm-networking-4" src="https://github.com/user-attachments/assets/0b75e55e-7168-4c0d-948b-cb838e42e741"></td>
  <tr>
    <td>Step 1</td>
    <td>Step 2</td>
    <td>Step 3</td>
    <td>Step 4</td>
  </tr>
</table>



🔷***Set DC-1’s Private IP Address to Static***  
*To ensure consistent network configuration, set DC-1's private IP to static.*

- Azure Portal > **Virtual Machines** > **DC-1** > **Networking** > **IP Configuration**.
     - Set **Private IP** to **Static** and click **Save**.
<table>
  <tr>
    <td><img width="200" height="150" alt="dc1-static-nic-9" src="https://github.com/user-attachments/assets/db68dab4-06ae-4950-b37f-da736ef6656a">
</td>
    <td><img width="200" height="150" alt="dc1-static-nic-10" src="https://github.com/user-attachments/assets/28aae395-6359-4b45-b399-0728b8a70038">
</td>
   
  <tr>
    <td>Step 1</td>
    <td>Step 2</td>
  </tr>
</table>

🔷***Disable the Windows Firewall on DC-1***  
*Disable the firewall to allow client connections to the domain during the lab.*

- Log in to **DC-1** via Remote Desktop.
- Open `wf.msc` from the Run command.
    - Go to **Windows Defender Firewall Properties**.
    - Set the Firewall state to **Off** for Domain, Private, and Public Profiles.
    - Apply the changes.
 <table>
  <tr>
    <td><img width="200" height="150" alt="how-to-view-ip-14" src="https://github.com/user-attachments/assets/9f3770f2-2833-4b29-a816-1d0af887d6c0"><img width="200" height="150" alt="dc1-RDP-1" src="https://github.com/user-attachments/assets/0e2666db-0d2e-45e9-b73f-0a6be357034e"></td>
    <td><img width="200" height="150" alt="windows-firewall0run-12" src="https://github.com/user-attachments/assets/63db9cb9-9187-4164-89ec-cc2b20c8e761"><img width="200" height="150" alt="windows-firewall-run-13" src="https://github.com/user-attachments/assets/5fa21321-b8f2-4782-bb3a-97bde36aa0bd"></td>
  
  <tr>
    <td>Step 1</td>
    <td>Step 2</td>
  </tr>
</table>

🔷***Create the Client-1 VM***  
*Set up a client virtual machine for testing domain connectivity. Ensure the virtual machine uses the previously created resource group and virtual network.*

- **Resource Group**: Active-Directory-Lab
- **Name**: Client-1
- **Region**: (US) East US 2
- Image: Windows 10 Pro (at least 2 vCPUs)
- Username: labuser, Password: Cyberlab123!
- **Virtual Network**: Active-Directory-VNet; *leave subnet as default*.

🔷***Set Client-1 to Use DC-1 as DNS***  
*Configure Client-1’s DNS to point to DC-1’s private IP so it can locate the domain controller for authentication and network services.*

- Azure Portal > **Virtual Machines** > **Client-1** > **Networking** > **DNS Servers**.
    - Set to **Custom** and enter DC-1’s static private IP.
    - Save the settings.

🔷***Restart Client-1***  
*Apply the new DNS settings by restarting Client-1.*

- Azure Portal > **Virtual Machines** > **Client-1** > **Restart**.

🔷***Ping DC-1 from Client-1*** 
*Verify network connectivity between Client-1 and DC-1.*

- Open **Command Prompt** or **PowerShell** on **Client-1**.
   - Run the following command:  
  `ping <DC-1-private-IP-address>`.

***Deploying Active Directory***
---

🔶***Install Active Directory on DC-1***  
*Set up Active Directory to make DC-1 a domain controller.*

- Log in to **DC-1** via RDP.
- Open **Server Manager** > **Add Roles and Features**.
    - Select **Active Directory Domain Services** > **Add Features** > **Install**.
- After installation, click **Promote this server to a domain controller**.
    - Select **Add a new forest** with root domain name: **mydomain.com**.
    - Set up a username/password, de-select **Create DNS delegation**, and click **Install**.
    - After restart, log in as `mydomain.com\labuser`.

🔶***Create Organizational Units (OUs)***  
*Organize resources within the domain by creating Organizational Units.*

- Open **Active Directory Users and Computers (ADUC)**.
- Right-click **mydomain.com** > **New** > **Organizational Unit**.
    - Create the following OUs:
      - `_EMPLOYEES`
      - `_ADMINS`
      - `_CLIENTS`

🔶***Create a Domain Admin User*** 
*Create a user with domain admin privileges.*

- In **_ADMINS**, right-click > **New** > **User**.
    - Create user: **Jane Doe** with the username: **jane_admin**.
    - Right-click **Jane Doe** > **Properties** > **Member Of** > **Add**: **Domain Admins**.

🔶***Log out and log back in as Jane Doe***  
*Use the domain admin account for future configurations.*

- Log out of **DC-1**.
- Log back in as `mydomain.com\jane_admin`.

🔶***Join Client-1 to the Domain***  
*Add Client-1 to the domain for centralized management.*

- Log in to **Client-1** as **labuser** via RDP.
    - Open **System** > **Rename this PC (advanced)**.
    - Under **Member of**, enter: **mydomain.com**.
    - Click **OK** and restart **Client-1**.
- Verify **Client-1** appears in **ADUC** under **mydomain.com > Computers**.
- Move **Client-1** to the **_CLIENTS** OU.

🔶***Set Up Remote Desktop for Non-Admin Users on Client-1***  
*Enable domain users to access Client-1 via Remote Desktop.*

- Log in to **Client-1** as `mydomain.com\jane_admin`.
- Open **System** > **Remote Desktop**.
    - Click **Select users that can remotely access this PC**.
    -  Add **Domain Users** > Click **OK**.

🔶***Create Additional Users and Log In to Client-1***  
*Create multiple users in the _EMPLOYEES OU for testing.*

- Log in to **DC-1** as `jane_admin`.
- Open **PowerShell ISE** as Administrator.
    - Run the [user creation script](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1) to generate users.
- Log in to **Client-1** with one of the generated accounts.

***Managing User Account Security and Logs in Active Directory***
---

🔷***Dealing with Account Lockouts***  
*Test the account lockout functionality and configure the policy.*

- Log into **DC-1**.
- Open **Active Directory Users and Computers**.
- Select a user in **_EMPLOYEES**, log into **Client-1**, and attempt incorrect passwords 10 times.
- After 10 attempts, use the correct password. No lockout occurs until Group Policy is configured.

🔷***Configure Group Policy to Lock Out Accounts After 5 Failed Attempts***  
*Apply an account lockout policy to prevent brute force attacks.*

- Log in to **DC-1** as `jane_admin`.
- Open **Group Policy Management Console**.
- Right-click **Default Domain Policy** > **Edit**.
    - Navigate to **Computer Configuration** > **Policies** > **Windows Settings** > **Security Settings** > **Account Policies** > **Account Lockout Policy**.
    - Set **Account Lockout Duration** to **30 minutes**.
    - Set **Account Lockout Threshold** to **5 invalid logon attempts**.
    - Set **Reset Account Lockout Counter After** to **10 minutes**.
- Run `gpupdate /force` on **Client-1** to apply the policy.

🔷***Test the Lockout Policy*** 
*Verify the account lockout after 5 failed login attempts.*

- Attempt incorrect passwords for a user on **Client-1**.
- After 5 attempts, the account will be locked.

🔷***Unlock the User Account***  
*Manually unlock a locked-out user.*

- On **DC-1**, open **Active Directory Users and Computers**.
    - Right-click the user and select **Unlock Account**.
    - Optionally reset the password.

🔷 ***Enable and Disable User Accounts***  
*Test account disabling and re-enabling functionality.*

- On **DC-1**, navigate to **_EMPLOYEES** in **ADUC**.
    - Right-click a user and select **Disable Account**.
- Attempt to log in on **Client-1** and observe the error.
- Re-enable the account and log in again.

🔷***Observing Logs***  
*Check logs for login failures and account lockouts.*

- Log in to **DC-1** or **Client-1** as `jane_admin`.
- Open **Event Viewer** > **Windows Logs** > **Security**.
    - Look for **Audit Failure** events with **Event ID 4625**.
 
<table>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  <tr>
    <td>Step 1</td>
    <td>Step 2</td>
    <td>Step 3</td>
    <td>Step 4</td>
  </tr>
</table>

