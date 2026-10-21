# Lab 07: Group Policy Basics

## Objective

Create a Group Policy Object (GPO), link it to an Organizational Unit (OU), and test a user policy in the Windows domain environment.

## Environment

- Domain controller: `WIN-SRV-DC01`
- Domain: `corp.northstar.local`
- Target OU: `NorthStar Users`
- Test user: `alex.employee`
- Client: `WIN-CLIENT01`

## Configuration and Testing

Created a GPO named `NorthStar Users - Test Policy` and linked it to the `NorthStar Users` OU using Group Policy Management.

Under `User Configuration > Policies > Administrative Templates > Start Menu and Taskbar`, enabled **Remove Run menu from Start Menu** to test the policy with `alex.employee`.

Confirmed that the Run command disappeared from the Start menu while the policy was enabled. The `Win + R` keyboard shortcut still opened the Run dialog.

After testing, changed the setting to **Not Configured** and ran `gpupdate /force` on the client. The update completed successfully, and the Run command worked normally again.

## Result

Tested how a GPO can apply a user setting to accounts in a targeted OU and confirmed that the setting could be returned to its default configuration after testing.
