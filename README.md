# Active Directory Configuration and Automation in Azure

## Overview

This repository details the process of refining a pre-existing Active Directory environment within Azure. It focuses on administrative user setup, client machine domain integration, remote desktop configuration for non-admin users, and automated mass user account creation using PowerShell. This builds upon an already deployed Windows Server 2022 Domain Controller (DC-1) and a Windows 10 client machine (Client-1), both residing within a configured Azure Virtual Network.

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

* Log in to your Domain Controller (DC-1) via Remote Desktop as a Domain Admin (adminuser / mydomain.com\adminuser)
* Create a Domain Admin user within the domain.
    * Click Start -> Windows Administrative Tools -> Active Directory Users and Computers
    * ![image](https://github.com/user-attachments/assets/377410e2-c9b1-4e37-9c72-a411908806ab)
    * In Active Directory Users and Computers (ADUC) right click on domain name (mydomain.com) on the left and click New -> Organizational Unit
    * ![image](https://github.com/user-attachments/assets/1f098630-64c5-4736-86df-c151d12d8496)
    * Create an Organizational Unit (OU) called “\_EMPLOYEES”.
    * ![image](https://github.com/user-attachments/assets/2a479e88-0979-42c5-a353-2e22622b09aa)
    * Create a new OU named “\_ADMINS”.
    * ![image](https://github.com/user-attachments/assets/e090dd0d-ad6b-45d7-a28c-e48ddec94da8)
    * Go to `_ADMINS` folder and right click -> New -> User
    * ![image](https://github.com/user-attachments/assets/498e24ec-e849-43b7-bf4b-dc4fb8c05c43)
    * Create a new employee named “Jane Doe” with the username of “jane_admin” and set a password
    * ![image](https://github.com/user-attachments/assets/ab6574db-88c5-4ae6-afee-068ad8d24c0e)
    * ![image](https://github.com/user-attachments/assets/0e16db82-92f4-41e5-a980-fd6403e1f97a)
    * Uncheck `User must change password at next login` and click next and finish
* Add jane_admin to the “Domain Admins” Security Group.
    * Go to Jane Doe in `_ADMINS` and right click and click Properties
    * ![image](https://github.com/user-attachments/assets/6504979f-49a0-42a0-bccf-55d3a4560894)
    * Select `Member Of` -> `Add` and enter "Domain Admins" anc click `Check Names` and Ok -> Apply -> Ok
    * ![image](https://github.com/user-attachments/assets/a2238c77-8d1b-4c31-9ff9-4160b2d0bde3)
    * Now Jane Doe is part of the Domain Admin Group
    * Log out out of DC-1 and log back in as “mydomain.com\jane_admin” to test
    * ![image](https://github.com/user-attachments/assets/e4ab7d33-bbc5-4ff7-9c07-d883468b0f2d)
    * Use jane_admin as the admin account from now on.
* Join Client-1 to the domain (mydomain.com).
    * Log into Client-1 (Windows 10 VM on the same VNET+RG as DC-1) with the VM's User (e.g `adminuser`) with it's Public IP
    * Open System Properties (Start -> System or run `sysdm.cpl`) and click `Rename this PC` on the right
    * ![image](https://github.com/user-attachments/assets/b60c6eee-6bdc-4ea3-bdaf-8d82c45dfdf1)
    * Under `Computer Name` click Change. Then under "Member of" select Domain and enter the domain name (mydomain.com) and click ok.
    * ![image](https://github.com/user-attachments/assets/c152d2a9-c7fb-458f-b087-6cf071a63c36)
    * If you get an Error its most likely that Client-1's DNS isn't DC-1's private IP address
    * Enter Admin credentials in the popup (adminuser, jane_admin)
    * ![image](https://github.com/user-attachments/assets/05778f69-293f-468d-a265-8e800428d688)
    * Hit ok to the popups and restart the computer for changes to take effect
    * Log into the Domain Controller and verify Client-1 shows up in ADUC under the "Computers" folder
    * ![image](https://github.com/user-attachments/assets/a2f87911-dad6-480a-b726-53abdfef5bbe)
    * For best structure and organization create a new OU named “_CLIENTS” and drag Client-1 into there. Click yes to the popup
    * ![image](https://github.com/user-attachments/assets/c27fa12e-5184-4746-b030-172b44045e71)

### Part 2: Setting Up Remote Desktop for Non-Admin Users

* RDP into Client-1 as the newly added Domain User (mydomain.com\jane_admin)
* If you can't then go back through the steps to check Client-1 was added to the Domain and that the User was added to the Domain Admins Group
* Click Start -> System -> Related Settings | Remote Desktop
* ![image](https://github.com/user-attachments/assets/fd0738e8-9d18-494b-a3f6-124bba6cbbc0)
* Click `Select users that can remotely access this PC`
* ![image](https://github.com/user-attachments/assets/266e4b6b-560a-4129-a387-b77c6c18f1c8)
* Add Domain Users to the allowed users.
* ![image](https://github.com/user-attachments/assets/d1636211-8a5f-4feb-8995-d57b2bcd9dd6)

### Part 3: Creating Multiple User Accounts with PowerShell

* Log into DC-1 as `mydomain.com\jane_admin`.
* Open PowerShell ISE as an administrator.
* Open the script notepad and paste the provided PowerShell script.
```
 # ----- Edit these Variables for your own Use Case ----- #
$PASSWORD_FOR_USERS   = "Password1"
$NUMBER_OF_ACCOUNTS_TO_CREATE = 1000
# ------------------------------------------------------ #

Function generate-random-name() {
    $consonants = @('b','c','d','f','g','h','j','k','l','m','n','p','q','r','s','t','v','w','x','z')
    $vowels = @('a','e','i','o','u','y')
    $nameLength = Get-Random -Minimum 3 -Maximum 7
    $count = 0
    $name = ""

    while ($count -lt $nameLength) {
        if ($($count % 2) -eq 0) {
            $name += $consonants[$(Get-Random -Minimum 0 -Maximum $($consonants.Count - 1))]
        }
        else {
            $name += $vowels[$(Get-Random -Minimum 0 -Maximum $($vowels.Count - 1))]
        }
        $count++
    }

    return $name

}

$count = 0
while ($count -lt $NUMBER_OF_ACCOUNTS_TO_CREATE) {
    $fisrtName = generate-random-name
    $lastName = generate-random-name
    $username = $fisrtName + '.' + $lastName
    $password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force

    Write-Host "Creating user: $($username)" -BackgroundColor Black -ForegroundColor Cyan
    
    New-AdUser -AccountPassword $password `
               -GivenName $firstName `
               -Surname $lastName `
               -DisplayName $username `
               -Name $username `
               -EmployeeID $username `
               -PasswordNeverExpires $true `
               -Path "ou=_EMPLOYEES,$(([ADSI]`"").distinguishedName)" `
               -Enabled $true
    $count++
}
```
* ![image](https://github.com/user-attachments/assets/3900ec1a-46c2-4bdc-9d08-b93a60cef746)
* Note: you can decide how many accounts are created by changing the variable `$NUMBER_OF_ACCOUNTS_TO_CREATE`
* Note: Ensure the spelling of "_EMPLOYEES" matches the Path defined in the script
* Note: The New-ADUser cmdlet creates a user in Active Directory aka creates a Domain User
* ![image](https://github.com/user-attachments/assets/17b0ed38-cd30-4562-a0f0-b920770ad481)
* Run the script by clicking the green triangle and observe the creation of user accounts in the terminal.
* ![image](https://github.com/user-attachments/assets/d2562d26-b84c-4e17-adf3-bdba858bfa28)
* Verify the created accounts in Active Directory Users and Computers (ADUC) > _EMPLOYEES
* ![image](https://github.com/user-attachments/assets/9706c36e-dee3-4fdb-95a2-fb9717aa61ab)
* Attempt to log into Client-1 with one of the created accounts (any of them)
* Remember password set in the script
* ![image](https://github.com/user-attachments/assets/bc73daad-ba6d-43e6-8287-ef263034272a)
* ![image](https://github.com/user-attachments/assets/9fc272ee-91c1-4565-8e8f-4a6ab7ce6dff)
* Can now log in via RDP with any domain user created

## Takeaways

* Configured remote desktop access for domain users on a Windows 10 client machine.
* Automated the creation of multiple user accounts using a PowerShell script.
* Demonstrated proficiency in Active Directory administration and PowerShell scripting.
* Understood how to create Organizational Units (OUs) and add users to security groups.
* Learned how to join a windows 10 client machine to a domain.
* Understood how to set remote desktop permissions for domain users.

