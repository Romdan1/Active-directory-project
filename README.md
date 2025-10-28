# Active-directory-project
# 🧩 Active Directory Virtual Lab

## 🖥️ Overview
This project demonstrates a **fully functional Active Directory (AD) environment** built using **Oracle VirtualBox**, **Windows Server 2019**, and **Windows 10**.  
The goal was to simulate a small enterprise network setup, automate user creation with PowerShell, and practice real-world domain management and troubleshooting skills.

---

## ⚙️ Lab Setup

### Virtual Machines
| Role | Hostname | OS | Function |
|------|-----------|----|-----------|
| Domain Controller | DC01 | Windows Server 2019 | Hosts AD DS, DNS, DHCP (optional) |
| Client Machine | PC01 | Windows 10 | Joined to domain for user login testing |

### Network Configuration
- Internal network for VM-to-VM communication  
- Static IP for Domain Controller: `192.168.1.10`  
- Client DNS points to DC: `192.168.1.10`  

---

## 🧠 Features Implemented
- Installed and configured **Active Directory Domain Services (AD DS)**  
- Created a new domain: `ramilab.local`  
- Configured **DNS** to support domain resolution  
- Used **PowerShell automation** to:
  - Create Organizational Units (OUs)  
  - Auto-generate users from a `names.txt` file  
  - Set passwords and enable accounts  
- Joined Windows 10 client VM to the domain  
- Tested authentication and Group Policy inheritance  

---

## 💻 PowerShell Automation
Example snippet used for automatic user creation:
```powershell
$PASSWORD_FOR_USERS   = "P@ssword123!"
$USER_FIRST_LAST_LIST = Get-Content .\names.txt
$password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force

New-ADOrganizationalUnit -Name _USERS -ProtectedFromAccidentalDeletion $false

foreach ($n in $USER_FIRST_LAST_LIST) {
    $parts = $n.Trim() -split '\s+'
    if ($parts.Count -lt 2) { continue }
    $first = $parts[0]; $last = $parts[1]
    $username = ($first.Substring(0,1) + $last).ToLower()
    New-ADUser -AccountPassword $password `
               -GivenName $first `
               -Surname $last `
               -Name $username `
               -Enabled $true `
               -Path "OU=_USERS,DC=ramilab,DC=local" `
               -PasswordNeverExpires $true
}
