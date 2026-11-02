# Lab 10 – Windows Security Auditing

## Objective

Configure Advanced Audit Policy to record successful and failed logon attempts, then review Windows Security events to understand how logon activity is recorded.

## Environment

- Domain: `corp.northstar.local`
- Domain Controller: `WIN-SRV-DC01`
- Operating System: Windows Server 2022
- Group Policy: `NorthStar - Windows Security Auditing`

## 1. Created a Dedicated Group Policy Object

Created and linked a new Group Policy Object named:

`NorthStar - Windows Security Auditing`

The GPO was linked to the Domain Controllers OU without modifying the existing Default Domain Controllers Policy or NorthStar PowerShell Logging policy.

## 2. Configured Advanced Audit Policy

Navigated to:

`Computer Configuration > Policies > Windows Settings > Security Settings > Advanced Audit Policy Configuration > System Audit Policies > Logon/Logoff`

Configured **Audit Logon** with the following settings:

- Success: Enabled
- Failure: Enabled

## 3. Enabled Advanced Audit Policy Override

Navigated to:

`Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > Security Options`

Enabled:

`Audit: Force audit policy subcategory settings (Windows Vista or later) to override audit policy category settings`

This ensures that advanced audit subcategory settings take precedence over conflicting basic audit policy settings.

## 4. Verified Audit Configuration

Used the following command to inspect the effective Logon audit policy:

`auditpol /get /subcategory:"Logon"`

The audit configuration was checked for successful and failed logon auditing.

## 5. Reviewed Security Event ID 4624

Opened Event Viewer and navigated to:

`Windows Logs > Security`

Filtered the log for Event IDs `4624` and `4625`.

Reviewed an Event ID 4624 entry and identified the following details:

| Field | Observed value |
|---|---|
| Event ID | 4624 |
| Security ID | SYSTEM |
| Account Name | WIN-SRV-DC01 |
| Account Domain | corp.northstar.local |
| Logon Type | 3 |
| Source Network Address | IPv6 link-local address (`fe80::...`) |

The event represented a successful network logon under the Local System security context.

## 6. Findings

- Event ID 4624 records successful logon attempts.
- Event ID 4625 records failed logon attempts.
- Logon Type 3 identifies a network logon.
- The SYSTEM security context can be associated with legitimate Windows system and computer operations.
- IPv6 link-local addresses beginning with `fe80::` are used for communication on the local network segment.
- Security events should be evaluated using their account, logon type, and network information rather than their Event ID alone.

No Event ID 4625 entries were observed during this review.

## Conclusion

Configured Windows Advanced Audit Policy to monitor successful and failed logon attempts on the domain controller.

Reviewed a successful network logon event and identified the associated computer account, SYSTEM security context, logon type, and source network address.

This exercise provided practical experience using Windows Security logs to review and interpret authentication activity.
