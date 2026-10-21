# Lab 02: Active Directory Domain Controller Setup

## Overview

Installed Active Directory Domain Services (AD DS) on `WIN-SRV-DC01` and promoted the server to a domain controller for `corp.northstar.local`.

## Configuration

- Server: `WIN-SRV-DC01`
- Domain: `corp.northstar.local`
- NetBIOS name: `CORP`
- Forest functional level: Windows Server 2016
- Domain functional level: Windows Server 2016
- DNS server: Installed
- Global Catalog: Enabled

## Setup and Verification

Installed the AD DS role through Server Manager and created a new forest for `corp.northstar.local`. The prerequisite check passed, and the server restarted after promotion.

Signed in with `CORP\Administrator` and opened Active Directory Users and Computers. Confirmed that the domain was listed and contained the default Users container, built-in Administrator and Guest accounts, and built-in security groups.
