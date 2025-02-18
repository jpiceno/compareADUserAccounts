# AD Comparison Tool

## Overview
The **AD Comparison Tool** is a PowerShell script designed to compare the **Active Directory (AD) group memberships** of two user accounts. This tool is useful for **troubleshooting user access issues** or ensuring group membership consistency across different users.

## How It Works
1. **Checks for the Active Directory module**
   - If the **Active Directory module** is not loaded, the script imports it.
2. **Prompts for two usernames**
   - The user needs to enter two Active Directory usernames.
3. **Retrieves user group memberships**
   - Fetches the **MemberOf** attribute for both accounts.
4. **Compares group memberships**
   - Identifies which groups are unique to each user.
5. **Formats the output**
   - Presents the **group membership differences** in a readable table.

## PowerShell Script
```powershell
If ( ! (Get-module ActiveDirectory )) {
    Import-Module ActiveDirectory
}

# Accounts to compare
$UserName1 = "enterUsernamehere"
$UserName2 = "enterUsernamehere2"

# Getting the AD User objects, including the MemberOf property
$userAccount1 = Get-ADUser -Identity $UserName1 -Properties MemberOf
$userAccount2 = Get-ADUser -Identity $UserName2 -Properties MemberOf

# Compare group memberships
$comparison = Compare-Object -ReferenceObject ($userAccount1).MemberOf -DifferenceObject ($userAccount2).MemberOf

# Replace the arrows produced by Compare-Object with meaningful text
$comparison | ForEach-Object  {
      if ($_.sideindicator -eq '<=') {
       $_.sideindicator = "Only $UserName1 is a member of"
      }

      if ($_.sideindicator -eq '=>'){
       $_.sideindicator = "Only $UserName2 is a member of"
      }
}

# Output a table that shows the difference in group membership
$comparison | Select-Object @{l='User';e={$_.SideIndicator}},@{l='Group';e={$_.InputObject}} | Format-Table -AutoSize
```

## Usage
1. **Modify the script** to replace `enterUsernamehere` and `enterUsernamehere2` with the actual Active Directory usernames.
2. **Run the script in PowerShell** with administrative privileges.
3. **View the output**, which will display group membership differences between the two users.

## Example Output
| User                         | Group                                  |
|------------------------------|----------------------------------------|
| Only User1 is a member of    | CN=Finance,OU=Groups,DC=Company,DC=com |
| Only User2 is a member of    | CN=HR,OU=Groups,DC=Company,DC=com      |

## Why Use This Tool?
✅ Quickly **identify missing group memberships** for troubleshooting.  
✅ Ensure **proper role-based access control (RBAC)**.  
✅ Validate **group assignments** during migrations or onboarding.

---
This tool simplifies **Active Directory user comparisons**, making it easier for IT admins to manage access rights efficiently.
