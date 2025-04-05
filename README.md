# Active Directory Domain Configuration in Azure

## Overview

This repository details the steps to configure Active Directory  on a Windows Server 2022 virtual machine (DC-1) in Azure, set up remote desktop access for non-administrative users on a Windows 10 client machine (Client-1), and create multiple user accounts using PowerShell.

## Prerequisites / Technologies Used

* Microsoft Azure Configured Environment:
    * Azure Portal (VM management, network configuration)
    * Azure Virtual Machines (Windows Server 2022, Windows 10)
    * Azure Virtual Network (main-vnet)
* Microsoft Windows Server 2022:
    * Active Directory Domain Services (AD DS) Installed
    * DC-1 promoted to Domain Controller
    * Server Manager
* Microsoft Windows 10:
    * Remote Desktop Protocol (RDP)
* PowerShell:
    * PowerShell ISE (Integrated Scripting Environment)
    * PowerShell scripting (user account creation)
* Active Directory Users and Computers (ADUC)
* Networking concepts (DNS, domain joining)

## Steps

### Part 1: Configuring the Domain Environment's Admins and Clients

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

* Configured remote desktop access for domain users on a Windows 10 client machine.
* Automated the creation of multiple user accounts using a PowerShell script.
* Demonstrated proficiency in Active Directory administration and PowerShell scripting.
* Understood how to create Organizational Units (OUs) and add users to security groups.
* Learned how to join a windows 10 client machine to a domain.
* Understood how to set remote desktop permissions for domain users.

