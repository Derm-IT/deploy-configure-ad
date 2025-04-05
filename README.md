# Active Directory Domain Deployment and Configuration in Azure

## Overview

This repository details the steps to install and configure Active Directory Domain Services (AD DS) on a Windows Server 2022 virtual machine (DC-1) in Azure, set up remote desktop access for non-administrative users on a Windows 10 client machine (Client-1), and create multiple user accounts using PowerShell.

## Prerequisites / Technologies Used

* Active Azure subscription with access to create virtual machines.
* A pre-configured virtual network and subnet in Azure, including DC-1 (Windows Server 2022) and Client-1 (Windows 10).
* Basic understanding of Active Directory concepts and PowerShell scripting.
* Understanding of Networking Topics (e.g. DNS)

## Steps

### Part 1: Install Active Directory and Promote DC-1

#### 1. Install Active Directory Domain Services (AD DS)

* Logged into DC-1 via RDP.
* Opened Server Manager and added the Active Directory Domain Services role.
* Completed the installation process.

#### 2. Promote DC-1 as a Domain Controller

* Promoted DC-1 to a domain controller, creating a new forest with the domain `mydomain.com`.
* Set the Forest and Domain Functional Levels to Windows Server 2022.
* Set a DSRM password.
* Waited for the server to restart and logged back in as `mydomain.com\labuser`.
* Created a Domain Admin user within the domain.
    * In Active Directory Users and Computers (ADUC), created an Organizational Unit (OU) called “\_EMPLOYEES”.
    * Created a new OU named “\_ADMINS”.
    * Created a new employee named “Jane Doe” (same password) with the username of “jane\_admin” / Cyberlab123!.
    * Added jane\_admin to the “Domain Admins” Security Group.
    * Logged out / closed the connection to DC-1 and logged back in as “mydomain.com\jane\_admin”.
    * Used jane\_admin as the admin account from now on.
* Joined Client-1 to the domain (mydomain.com).
    * From the Azure Portal, set Client-1’s DNS settings to the DC’s Private IP address (Already done).
    * From the Azure Portal, restarted Client-1 (Already done).
    * Logged into Client-1 as the original local admin (labuser) and joined it to the domain (computer will restart).
    * Logged into the Domain Controller and verified Client-1 shows up in ADUC.
    * Created a new OU named “\_CLIENTS” and drag Client-1 into there.

### Part 2: Setting Up Remote Desktop for Non-Admin Users

* Turned on the DC-1 and Client-1 VMs in the Azure Portal if they were off.
* Logged into Client-1 as `mydomain.com\jane_admin`.
* Opened System Properties (`sysdm.cpl`) and navigated to the Remote tab.
* Enabled remote connections to the computer and added Domain Users to the allowed users.
* Verified that users could now log into Client-1 remotely with domain credentials.

### Part 3: Creating Multiple User Accounts with PowerShell

* Logged into DC-1 as `jane_admin`.
* Opened PowerShell ISE as an administrator.
* Created a new script file and pasted the provided PowerShell script.
* Ran the script and observed the creation of user accounts.
* Verified the created accounts in Active Directory Users and Computers (ADUC).
* Attempted to log into Client-1 with one of the created accounts.

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
