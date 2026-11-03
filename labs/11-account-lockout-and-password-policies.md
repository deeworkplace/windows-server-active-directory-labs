# Lab 11 – Account Lockout and Password Policies

## Objective

Configure domain password requirements and account lockout settings using Group Policy. Verify the effective domain policy and test account lockout behavior using a dedicated Active Directory test account.

## Environment

- Domain: `corp.northstar.local`
- Domain Controller: `WIN-SRV-DC01`
- Client: `WIN-CLIENT01`
- Operating System: Windows Server 2022
- Group Policy: `NorthStar - Account Lockout and Password Policies`

## 1. Created and Linked a Dedicated GPO

Created a new Group Policy Object named:

`NorthStar - Account Lockout and Password Policies`

Linked the GPO at the domain root of `corp.northstar.local`.

The existing Group Policy Objects were left unchanged.

## 2. Configured Password Policy

Configured the following settings under:

`Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Password Policy`

| Policy | Configuration |
|---|---|
| Enforce password history | 24 passwords |
| Maximum password age | Never expires (0 days) |
| Minimum password age | 1 day |
| Minimum password length | 14 characters |
| Password must meet complexity requirements | Enabled |
| Store passwords using reversible encryption | Disabled |

The minimum password length audit and relax minimum password length limits settings were left Not Defined.

![Password Policy Configuration](screenshots/lab-11-password-policy.jpg)

## 3. Configured Account Lockout Policy

Configured the following settings under:

`Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Account Lockout Policy`

| Policy | Configuration |
|---|---|
| Account lockout threshold | 10 invalid attempts |
| Account lockout duration | 15 minutes |
| Reset account lockout counter after | 15 minutes |

![Account Lockout Policy Configuration](screenshots/lab-11-account-lockout-policy.jpg)

## 4. Applied and Verified Group Policy

Applied the updated Group Policy configuration using:

`gpupdate /force`

Verified the applied GPO using:

`gpresult /r /scope computer`

Also used the following command to review the effective domain account policy:

`net accounts /domain`

![Effective Domain Password and Lockout Policy](screenshots/lab-11-effective-domain-policy.jpg)

## 5. Created a Test Domain Account

Created a dedicated test account named `LabLockoutTest` under the `NorthStar Users` organizational unit.

The account was configured as a standard domain user without administrative privileges.

A password meeting the configured password requirements was assigned.

## 6. Tested Account Lockout

Used `WIN-CLIENT01` to perform controlled failed sign-in attempts against the `LabLockoutTest` account.

### Monitored Account Status Using PowerShell

On `WIN-SRV-DC01`, used the Active Directory PowerShell module to check the account's lockout status and failed password count.

Executed:

```powershell
Get-ADUser -Identity LabLockoutTest -Properties LockedOut,BadPwdCount |
Format-List SamAccountName,LockedOut,BadPwdCount
```

![PowerShell Account Lockout Status](screenshots/lab-11-powershell-account-lockout-status.jpg)

The initial check after six failed sign-in attempts returned:

- SamAccountName: `LabLockoutTest`
- LockedOut: `False`
- BadPwdCount: `6`

Continued testing showed the failed password count increasing from 6 to 10. Once the configured threshold was reached, the account status changed to:

- LockedOut: `True`

The `Get-ADUser` command provided a way to monitor the account's lockout status and failed password count directly through Active Directory.

This confirmed that the domain account lockout policy was functioning as configured.

## 7. Reviewed Security Event ID 4740

Opened Event Viewer on `WIN-SRV-DC01` and reviewed the Security log.

Filtered for Event ID `4740`, which records account lockout events.

Captured the event details associated with the `LabLockoutTest` account.

![Account Lockout Event ID 4740](screenshots/lab-11-account-lockout-event-4740.jpg)

## 8. Unlocked the Test Account

After completing the lockout test, unlocked `LabLockoutTest` through Active Directory Users and Computers.

Steps performed:
1. Opened Active Directory Users and Computers on WIN-SRV-DC01.
2. Navigated to corp.northstar.local → NorthStar Users.
3. Right-clicked LabLockoutTest and selected Properties.
4. Opened the Account tab.
5. Selected Unlock account.
6. Clicked Apply, then OK.

The account was successfully unlocked and restored for future lab exercises.

## Findings

- Domain password requirements can be centrally managed through Group Policy.
- Account lockout settings help protect domain accounts against repeated failed authentication attempts.
- Event ID 4740 provides a record of account lockout activity.
- The `LockedOut` and `BadPwdCount` properties can help administrators investigate account lockout conditions.
- A dedicated test account allows lockout behavior to be tested without risking administrative accounts.

## Conclusion

Configured domain password and account lockout policies through a dedicated Group Policy Object.

Verified the applied policy, tested account lockout using a dedicated domain account, reviewed Event ID 4740, and restored the test account through Active Directory Users and Computers.

Verified the effective domain policy and performed a controlled lockout test using a dedicated domain account. Reviewed the resulting Security event and restored the test account through Active Directory Users and Computers.

The lab provided hands-on experience with domain password management, account lockout controls, and basic account lockout investigation.
