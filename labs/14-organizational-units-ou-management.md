# Organizational Units (OU) Management

## Overview

This lab expands the existing Active Directory structure by creating Organizational Units (OUs) for user departments and computer accounts. Existing user and computer accounts were moved into their new OUs, and PowerShell was used to verify their locations.

The lab also covers protection against accidental deletion and checks whether the existing Group Policy configuration continues to apply after moving a workstation.

## Objectives

- Create parent and child Organizational Units.
- Organize user accounts by department and computer accounts by function.
- Enable protection against accidental deletion.
- Move existing Active Directory objects into their designated OUs.
- Verify OU structure and object locations using PowerShell.
- Check Group Policy processing after moving a workstation.

## Lab Environment

| Component | Configuration |
|---|---|
| Domain | `corp.northstar.local` |
| Domain Controller | `WIN-SRV-DC01` |
| Operating System | Windows Server 2022 |
| Client Computer | `WIN-CLIENT01` |
| Client Operating System | Windows 11 |
| Virtualization | Oracle VirtualBox |

## 1. Review Existing Active Directory Objects

Before creating the new OUs, the existing Active Directory structure was reviewed.

The following objects were identified:

- `NorthStar Users` - Existing OU at the domain root.
- `LabLockoutTest` - Existing test user account.
- `Lab13-Workstations` - Security group used for Lab 13 GPO security filtering.
- `WIN-CLIENT01` - Domain-joined Windows 11 workstation.
- `WIN-SRV-DC01` - Domain controller located in the Domain Controllers OU.

The `Lab13-Workstations` security group was left in its original location because it was still being used by the Lab 13 Group Policy configuration.

## 2. Create User Organizational Units

Four child OUs were created under the existing `NorthStar Users` OU:

- `IT`
- `HR`
- `Finance`
- `Lab Accounts`

The departmental OUs provide separate locations for user accounts, while `Lab Accounts` keeps test accounts separate from regular user accounts.

## 3. Create Computer Organizational Units

A new OU named `NorthStar Computers` was created at the domain root.

Two child OUs were added:

- `Workstations`
- `Servers`

This separates workstation accounts from server accounts instead of keeping them together in the default Computers container.

The resulting OU structure is shown below.

```text
corp.northstar.local
│
├── Domain Controllers
│   └── WIN-SRV-DC01
│
├── NorthStar Users
│   ├── IT
│   ├── HR
│   ├── Finance
│   └── Lab Accounts
│
└── NorthStar Computers
    ├── Workstations
    └── Servers
```

![Completed Active Directory OU structure](../screenshots/lab-14-ou-structure.jpg)

## 4. Enable Protection Against Accidental Deletion

Protection against accidental deletion was enabled on the newly created OUs.

This setting helps prevent an OU from being deleted by mistake. If an administrator needs to delete a protected OU, the protection must first be disabled.

For existing OUs, the setting can be accessed through the **Object** tab in the OU properties. Advanced Features must be enabled in Active Directory Users and Computers before this tab appears.

![OU protection against accidental deletion](../screenshots/lab-14-ou-protection.jpg)

## 5. Move Existing Active Directory Objects

After creating the new OUs, the existing test user and workstation computer account were moved into their designated locations.

### Move User Account

The `LabLockoutTest` account was moved into:

`NorthStar Users > Lab Accounts`

![LabLockoutTest account in the Lab Accounts OU](../screenshots/lab-14-user-object-location.jpg)

### Move Computer Account

The `WIN-CLIENT01` computer account was moved into:

`NorthStar Computers > Workstations`

The domain controller `WIN-SRV-DC01` remained in the built-in Domain Controllers OU. The `Lab13-Workstations` security group was also left in its original location.

![WIN-CLIENT01 computer account in the Workstations OU](../screenshots/lab-14-computer-object-location.jpg)

Moving an object changes its location in Active Directory and updates its Distinguished Name (DN). Its Security Identifier (SID) does not change.

## 6. Verify OU Structure and Object Locations Using PowerShell

PowerShell was used to check the new OU structure and confirm that the user and computer accounts were in the correct locations.

### List Organizational Units

```powershell
Get-ADOrganizationalUnit -Filter * |
Select-Object Name, DistinguishedName |
Format-Table -AutoSize
```

### Check User Account Location

```powershell
Get-ADUser -Identity LabLockoutTest -Properties DistinguishedName |
Select-Object Name, SamAccountName, DistinguishedName
```

Expected Distinguished Name:

```text
CN=LabLockoutTest,OU=Lab Accounts,OU=NorthStar Users,DC=corp,DC=northstar,DC=local
```

### Check Computer Account Location

```powershell
Get-ADComputer -Identity WIN-CLIENT01 -Properties DistinguishedName |
Select-Object Name, DistinguishedName
```

Expected Distinguished Name:

```text
CN=WIN-CLIENT01,OU=Workstations,OU=NorthStar Computers,DC=corp,DC=northstar,DC=local
```

The output was reviewed to confirm the new locations of both objects.

![PowerShell verification of OU structure and object locations](../screenshots/lab-14-powershell-ou-verification.jpg)

## 7. Verify Group Policy Inheritance

After moving `WIN-CLIENT01` into the `Workstations` OU, its Group Policy processing was checked.

In Lab 13, the `NorthStar - Advanced Group Policy Security Settings` GPO was linked at the domain level. Its security filtering targets the `Lab13-Workstations` security group.

Since `WIN-CLIENT01` remained a member of that group, moving it into the new OU should not prevent it from receiving the domain-linked GPO.

The workstation was restarted, and the following commands were run from an elevated Command Prompt.

**Refresh Group Policy:**

```powershell
gpupdate /force
```

**Check applied computer policies:**

```powershell
gpresult /r /scope computer
```

The output was checked for `NorthStar - Advanced Group Policy Security Settings` under Applied Group Policy Objects.

![Group Policy inheritance verification after moving WIN-CLIENT01](../screenshots/lab-14-gpo-inheritance-verification.jpg)

## 8. Final Active Directory Structure

The final structure separates user accounts by department and computer accounts by function.

```text
corp.northstar.local
│
├── Domain Controllers
│   └── WIN-SRV-DC01
│
├── NorthStar Users
│   ├── IT
│   ├── HR
│   ├── Finance
│   └── Lab Accounts
│       └── LabLockoutTest
│
└── NorthStar Computers
    ├── Workstations
    │   └── WIN-CLIENT01
    └── Servers
```

## What I Learned

### Organizational Units and Active Directory

1. **Organizing Active Directory with OUs:** Learned how to create parent and child OUs to separate user accounts by department and computer accounts by function.

2. **Managing Active Directory Objects:** Learned how to move existing user and computer accounts into different OUs and check their new locations using Distinguished Names (DNs).

3. **Protecting OUs:** Learned how to enable protection against accidental deletion and why it is important when managing the Active Directory structure.

4. **Understanding Group Policy Inheritance:** Learned how moving a computer into another OU affects its Group Policy scope. Domain-linked GPOs can still apply through inheritance, as long as the computer meets the policy's filtering requirements.

### Group Policy Concepts

5. **GPO Linking:** Determines where a Group Policy Object is assigned in Active Directory. For example, a GPO linked at the domain level can apply to eligible users and computers throughout the domain.

6. **GPO Inheritance:** Allows policies linked at a parent level to flow down to child OUs. Moving a computer into another OU does not automatically remove policies inherited from the domain.

7. **Security Filtering:** Controls which users or computers can apply a GPO. The targeted account needs both **Read** and **Apply Group Policy** permissions. In Lab 13, the `Lab13-Workstations` group was used to target the workstation.

8. **WMI Filtering:** Checks conditions on a computer before allowing a GPO to apply. For example, a WMI filter can use the operating system ProductType value to distinguish between Windows client computers (`1`), domain controllers (`2`), and member servers (`3`).

## Key Takeaways

- OUs keep Active Directory users and computers organized.
- Nested OUs make it possible to create a structure based on departments and computer roles.
- GPOs can be linked to OUs, domains, and sites, but not directly to ordinary AD containers.
- Protection against accidental deletion helps prevent unintended changes to the OU structure.
- Moving an object changes its Distinguished Name but does not change its SID.
- GPO linking determines where a policy is assigned, while inheritance allows it to flow down the hierarchy.
- Security filtering and WMI filtering provide additional conditions that determine whether a GPO applies.
