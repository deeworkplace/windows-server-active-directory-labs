# Lab 09 – PowerShell Logging Investigation

## Objective

Review PowerShell Script Block Logging events in Event Viewer and identify the recorded commands, script paths, and user accounts.

## Environment

- Domain: `corp.northstar.local`
- Domain Controller: `WIN-SRV-DC01`
- Operating System: Windows Server 2022
- Group Policy: `NorthStar - PowerShell Logging`
- Event Log: Microsoft-Windows-PowerShell/Operational

## 1. Opened the PowerShell Operational Log

Opened Event Viewer and navigated to:

`Applications and Services Logs > Microsoft > Windows > PowerShell > Operational`

Reviewed the available PowerShell events.

## 2. Filtered Event ID 4104

Used **Filter Current Log** to display events with Event ID `4104`.

Event ID 4104 records PowerShell script block activity when Script Block Logging is enabled.

## 3. Reviewed the Lab 08 Test Event

Located the event generated during Lab 08 using the following command:

`Write-Output "Lab 08 Script Block Logging test"`

Event details:

- Event ID: `4104`
- User: `CORP\Administrator`
- ScriptBlockText: `Write-Output "Lab 08 Script Block Logging test"`
- Path: Blank
- ScriptBlockId: Unique identifier for the script block

The Path field was blank because the command was entered directly in the PowerShell console rather than run from a script file.

## 4. Reviewed a Windows Diagnostic Script Event

Reviewed another Event ID 4104 recorded at 2:03:40 PM.

The event referenced the following script:

`C:\Windows\Temp\sdiag_6b17f...\TS_WERQueue.ps1`

Additional details:

- Event ID: `4104`
- Script block: 1 of 1
- User: `CORP\Administrator`

The script name and temporary directory were consistent with Windows diagnostic activity. The event was not treated as suspicious based on its name or path alone.

## 5. Key Findings

- Event ID 4104 records PowerShell script block activity.
- ScriptBlockText contains the content recorded by PowerShell.
- ScriptBlockId provides a unique identifier for a script block.
- The Path field can identify a script file when one is associated with the event.
- A blank Path can occur when commands are entered interactively.
- PowerShell events can include activity from automated Windows processes as well as administrator commands.
- The account associated with an event does not necessarily identify who manually initiated the activity.

## Conclusion

Reviewed two PowerShell Script Block Logging events and verified that the test command from Lab 08 was recorded successfully.

Also reviewed a separate event referencing a Windows diagnostic script. Compared the event contents, script path, and user information to understand how PowerShell activity is recorded.

This exercise provided practical experience using Event ID 4104 to review PowerShell activity in Windows Server.
