# Active Directory Domain Deployment and Configuration in Azure

## Overview

This repository details the steps to install and configure Active Directory Domain Services (AD DS) on a Windows Server 2022 virtual machine (DC-1) in Azure, set up remote desktop access for non-administrative users on a Windows 10 client machine (Client-1), and create multiple user accounts using PowerShell.

## Prerequisites / Technologies Used

* Microsoft Azure Configured Environment:
    * Azure Portal (VM management, network configuration)
    * Azure Virtual Machines (Windows Server 2022, Windows 10)
    * Azure Virtual Network (main-vnet)
* Microsoft Windows Server 2022:
    * Active Directory Domain Services (AD DS)
    * Server Manager
* Microsoft Windows 10:
    * Remote Desktop Protocol (RDP)
* PowerShell:
    * PowerShell ISE (Integrated Scripting Environment)
    * PowerShell scripting (user account creation)
* Active Directory Users and Computers (ADUC)
* Networking concepts (DNS, domain joining)

## Steps

### Part 1: Install Active Directory and Promote DC-1

#### 1. Install Active Directory Domain Services (AD DS)

* Log into DC-1 via Remote Desktop.
* Open Server Manager and click `Add roles and features` on Dashboard
* ![image](https://github.com/user-attachments/assets/66d6a801-0543-40d2-b94b-dedeab15856a)
* Next to server selection and pick `DC-1`
* ![image](https://github.com/user-attachments/assets/b6f2ced9-41b5-4264-8a50-c87da634e3ce)
* Server roles: check Active Directoy Domain Services and click add features
* ![image](https://github.com/user-attachments/assets/0b3bea1a-aae5-435f-823d-58318e16b3e9)
* Click next until confirmation and check restart box
* ![image](https://github.com/user-attachments/assets/238589af-b8c1-4586-9e42-b7da90a34dfc)
* Click install and wait
* Complete the installation process.

#### 2. Promote DC-1 as a Domain Controller

* Go back to the Dashboard and notice the new `Rules and Server Groups Created`
* Click the flag in the top right to open Post-Deployment Configuration notification
* ![image](https://github.com/user-attachments/assets/b44d1eeb-6498-4154-9dc2-8ffe86fb43a9)
* Promote DC-1 to a domain controller, check create a new forest and name it `mydomain.com`.
* ![image](https://github.com/user-attachments/assets/978b514f-bcf2-4ba4-ae51-39bc6d11637d)
* Head to Domain Controller Option and Set a DSRM password. Leave everything the same and click next
* ![image](https://github.com/user-attachments/assets/63610e09-30aa-48a7-9064-c78761b3a710)
* Uncheck DNS delegation and click next until Prerequisites Check and click Install
* Wait for the server to restart
* Reconnect with RDP but this time log in as a domain user
   * To do that login as <domain-name\<domain-user>, so log back in as `mydomain.com\adminuser`.
   * ![image](https://github.com/user-attachments/assets/a8e17459-1db2-4086-b4ee-c3efe3d5776a)
   * Note: The account used to make DC-1 a DC is automatically a domain user so `adminuser` would also work
* Open Command Prompt as Admin and run `WMIC COMPUTERSYSTEM GET DOMAINROLE` to check if DC-1 is not a DC
* If it says 5 it worked, reference below on what each number means
* ![image](https://github.com/user-attachments/assets/a30765db-43d8-4e07-a465-cf5acc5372ca)
* ![image](https://github.com/user-attachments/assets/51c1445c-7393-47aa-b8db-8c806f132a84)

DC-1 has now been successfully set up as Domain Controller running Active Directory Domain Services!

* Create a Domain Admin user within the domain.
    * In Active Directory Users and Computers (ADUC), create an Organizational Unit (OU) called “\_EMPLOYEES”.
    * Create a new OU named “\_ADMINS”.
    * Create a new employee named “Jane Doe” (same password) with the username of “jane\_admin” / Cyberlab123!.
    * Add jane\_admin to the “Domain Admins” Security Group.
    * Log out / close the connection to DC-1 and log back in as “mydomain.com\jane\_admin”.
    * Use jane\_admin as the admin account from now on.
* Join Client-1 to the domain (mydomain.com).
    * From the Azure Portal, set Client-1’s DNS settings to the DC’s Private IP address (Already done).
    * From the Azure Portal, restart Client-1 (Already done).
    * Log into Client-1 as the original local admin (labuser) and join it to the domain (computer will restart).
    * Log into the Domain Controller and verify Client-1 shows up in ADUC.
    * Create a new OU named “\_CLIENTS” and drag Client-1 into there.

### Part 2: Setting Up Remote Desktop for Non-Admin Users

* Turn on the DC-1 and Client-1 VMs in the Azure Portal if they were off.
* Log into Client-1 as `mydomain.com\jane_admin`.
* Open System Properties (`sysdm.cpl`) and navigate to the Remote tab.
* Enable remote connections to the computer and add Domain Users to the allowed users.
* Verify that users can now log into Client-1 remotely with domain credentials.

### Part 3: Creating Multiple User Accounts with PowerShell

* Log into DC-1 as `jane_admin`.
* Open PowerShell ISE as an administrator.
* Create a new script file and paste the provided PowerShell script.
* Run the script and observe the creation of user accounts.
* Verify the created accounts in Active Directory Users and Computers (ADUC).
* Attempt to log into Client-1 with one of the created accounts.

## Takeaways

* Successfully installed and configured Active Directory Domain Services on a Windows Server 2022 VM in Azure.
* Promoted the server to a domain controller and created a new domain.
* Configured remote desktop access for domain users on a Windows 10 client machine.
* Automated the creation of multiple user accounts using a PowerShell script.
* Demonstrated proficiency in Active Directory administration and PowerShell scripting.
* Understood how to create Organizational Units (OUs) and add users to security groups.
* Learned how to join a windows 10 client machine to a domain.
* Understood how to set remote desktop permissions for domain users.
* Understood how to create mass user accounts using powershell.
* Emphasized the importance of not deleting the VMs for future projects, and the proper way to save money on Azure VMs, by stopping them.
