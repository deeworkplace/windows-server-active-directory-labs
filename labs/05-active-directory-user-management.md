# Lab 05: Active Directory User Management

## Overview

Created an organizational unit and a user account in Active Directory, then tested the account on a domain-joined Windows client.

## Configuration

- Domain controller: `WIN-SRV-DC01`
- Domain: `corp.northstar.local`
- Organizational unit: `NorthStar Users`
- User account: `alex.employee`
- Client computer: `WIN-CLIENT01`

## Configuration and Testing

Created the `NorthStar Users` organizational unit in Active Directory Users and Computers and added the `alex.employee` account.

Signed in to `WIN-CLIENT01` with `CORP\alex.employee` and ran `whoami`. The command returned `corp\alex.employee`, confirming that the user was signed in with the domain account.
