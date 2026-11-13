# Lab 16: Active Directory File Permissions and Departmental Access Management

## Overview

This lab focuses on managing file and folder permissions in a Windows Server Active Directory environment. It demonstrates NTFS permission inheritance, department-specific access, network share permissions, and group-based access management.

The exercise uses separate IT and HR folders to demonstrate how permissions can be configured independently to prevent unnecessary access between departments.

## Lab Environment

- **Domain:** `corp.northstar.local`
- **Domain Controller:** `WIN-SRV-DC01`
- **Client Computer:** `WIN-CLIENT01`
- **Operating System:** Windows Server 2022 and Windows 11
- **Virtualization:** Oracle VirtualBox

## Objectives

- Understand NTFS permission inheritance.
- Examine inherited and explicit permissions.
- Disable inheritance and convert inherited permissions into explicit permissions.
- Configure department-specific security groups.
- Assign NTFS and network share permissions.
- Test folder access from a domain-joined client.
- Verify access revocation and restoration.
- Apply the principle of least privilege.

## 1. Create the Department Folder Structure

Created a folder structure under the existing `C:\IT-Shared` directory.

```text
C:\IT-Shared
└── Lab16-FilePermissions
    ├── IT
    └── HR
```

The IT and HR folders provide separate locations for department-specific resources.

**Screenshot:**

- `../screenshots/lab-16-folder-structure.jpg`

## 2. Examine NTFS Permission Inheritance

Inspected the HR folder's Advanced Security Settings to identify inherited permissions.

The HR folder initially inherited permissions from its parent directory, `C:\IT-Shared`. This included the `DL-IT-Resources` security group with Modify access.

This demonstrated how permissions assigned to a parent folder can propagate to its child folders.

### Disable Inheritance

Disabled permission inheritance on the HR folder and selected the option to convert inherited permissions into explicit permissions.

This preserved the existing permission entries while allowing the HR folder's permissions to be managed independently.

The `DL-IT-Resources` entry was then removed from the HR folder.

**Key observation:** Disabling inheritance does not automatically remove inherited permissions when they are converted into explicit entries. The entries must be reviewed and modified as needed.

## 3. Create a Department-Specific Security Group

Created a new security group in the existing `NorthStar Users\Security Groups` OU.

| Setting | Configuration |
|---|---|
| Group name | `DL-HR-Resources` |
| Group scope | Domain Local |
| Group type | Security |

The group was created to manage access to HR resources independently of the existing IT security group.

**Screenshot:**

- `../screenshots/lab-16-hr-domain-local-group.jpg`

## 4. Configure HR NTFS Permissions

Added `DL-HR-Resources` to the HR folder's Security permissions.

Assigned the following permission:

| Security Group | Permission | Access Type |
|---|---|---|
| `DL-HR-Resources` | Modify | Allow |

Verified that `DL-IT-Resources` was no longer listed in the HR folder's permission entries.

The HR folder now had an independent permission configuration.

**Screenshots:**

- `../screenshots/lab-16-hr-ntfs-permissions.jpg`
- `../screenshots/lab-16-hr-permissions-verification.jpg`

## 5. Verify IT Folder Permissions

Inspected the IT folder's Advanced Security Settings.

The `DL-IT-Resources` group retained Modify permission inherited from `C:\IT-Shared`.

Unlike the HR folder, the IT folder continued using its inherited permissions.

This demonstrated how individual child folders can have different permission configurations under the same parent directory.

**Screenshot:**

- `../screenshots/lab-16-it-permissions-verification.jpg`

## 6. Assign HR Group Membership

Added the existing `LabLockoutTest` domain account to `DL-HR-Resources`.

The account received access through group membership rather than through an individual NTFS permission assignment.

The existing IT group membership was retained.

**Screenshot:**

- `../screenshots/lab-16-hr-group-membership.jpg`

## 7. Create a Separate HR Network Share

Created a separate network share for the HR folder.

| Setting | Configuration |
|---|---|
| Folder | `C:\IT-Shared\Lab16-FilePermissions\HR` |
| Share name | `HR-Shared` |

Configured the share permissions as follows:

| Security Group | Read | Change | Full Control |
|---|---|---|---|
| `DL-HR-Resources` | Allow | Allow | Not allowed |

The default `Everyone` entry was removed.

A separate HR share allows the HR department's network access to be managed independently of the existing IT share.

**Screenshots:**

- `../screenshots/lab-16-hr-share-permissions.jpg`
- `../screenshots/lab-16-hr-share-created.jpg`

## 8. Test HR Network Access

Signed in to `WIN-CLIENT01` using the `CORP\LabLockoutTest` account.

Accessed the HR network share:

```text
\\WIN-SRV-DC01\HR-Shared
```

The folder opened successfully.

This confirmed that the account could access the HR share through its membership in `DL-HR-Resources`.

**Screenshot:**

- `../screenshots/lab-16-client-hr-share-access.jpg`

## 9. Test HR Modify Permissions

Created a text file from the client computer inside the HR network share.

**Test file:** `HR-Access-Test.txt`

**File contents:**

```text
HR folder Modify permission successfully tested.
```

The file was successfully created and saved.

Verified that the file was visible on the server under:

```text
C:\IT-Shared\Lab16-FilePermissions\HR
```

This confirmed that the configured permissions allowed the test account to create and modify files through the network share.

**Screenshots:**

- `../screenshots/lab-16-hr-modify-access-test.jpg`
- `../screenshots/lab-16-client-hr-share-access2.jpg`

## 10. Verify Existing IT Access

Accessed the existing IT network share from `WIN-CLIENT01`:

```text
\\WIN-SRV-DC01\IT-Shared
```

Successfully opened `IT-Test-Document.txt`.

The IT access configuration remained functional after the HR permission changes.

The existing IT access structure uses the following group relationship:

```text
LabLockoutTest
      |
      v
GG-IT-Helpdesk
      |
      v
DL-IT-Resources
      |
      v
IT-Shared permissions
```

**Screenshot:**

- `../screenshots/lab-16-client-it-share-access.jpg`

## 11. Test HR Access Revocation

Temporarily removed `LabLockoutTest` from `DL-HR-Resources`.

Signed out of the client computer and signed back in to refresh the user's logon access token.

Attempted to access:

```text
\\WIN-SRV-DC01\HR-Shared
```

Windows returned an access-denied message because the account was no longer a member of the group assigned to the HR folder and share.

**Screenshot:**

- `../screenshots/lab-16-hr-access-revocation-test.jpg`

## 12. Verify IT Access After HR Access Revocation

While HR access was denied, accessed the existing IT network share again.

The account could still open `IT-Test-Document.txt`.

This demonstrated that removing access from the HR group did not affect the account's existing IT permissions.

The HR and IT folder permissions operated independently.

## 13. Restore HR Group Membership

Added `LabLockoutTest` back to `DL-HR-Resources`.

Signed out and back in to refresh the logon access token.

Successfully accessed `HR-Shared` again, confirming that HR access was restored.

The final lab configuration retains the test account's membership in both department access groups.

## Key Takeaways

### NTFS Permission Inheritance

- Child folders normally inherit permissions from their parent folders.
- Inherited permissions can be converted into explicit permissions.
- Removing inheritance does not automatically remove permissions when the conversion option is selected.
- Explicit permissions allow individual folders to have different access configurations.

### Share Permissions vs. NTFS Permissions

- Share permissions control access through a network share.
- NTFS permissions control access to files and folders at the file system level.
- Network access evaluates both permission types.
- The more restrictive effective permission determines the resulting network access.

### Group-Based Access Management

- Security groups simplify permission assignments.
- Users receive access through their group memberships.
- Domain Local groups can be used to assign permissions to resources within their domain.
- Group membership changes can grant or revoke access without changing the folder's permission configuration.

### Least Privilege

- Employees should receive only the access necessary for their responsibilities.
- Separate departmental groups help prevent unnecessary access to other departments' resources.
- Removing a user from one department's access group should not affect unrelated permissions.

### Access Troubleshooting

When a user cannot access a shared folder, verify:

1. The user's Active Directory group memberships.
2. Group nesting and security group configuration.
3. NTFS permissions.
4. Network share permissions.
5. Whether the user has signed out and back in after a membership change.

## Conclusion

This lab demonstrated how Active Directory security groups, NTFS permissions, and network share permissions work together to manage departmental file access.

The practical tests showed that a user could access and modify HR resources through group membership, lose HR access when removed from the corresponding group, and retain IT access independently.

These concepts are important for managing user access, maintaining resource security, and applying least-privilege principles in an IT environment.
