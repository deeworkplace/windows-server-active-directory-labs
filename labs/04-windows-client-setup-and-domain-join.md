# Lab 04: Windows Client Setup and Domain Join

## Overview

Installed Windows 11 Pro in VirtualBox and joined the client to the `corp.northstar.local` Active Directory domain.

## Environment

- Computer name: `WIN-CLIENT01`
- Operating system: Windows 11 Pro
- Virtualization: Oracle VirtualBox
- Domain controller: `WIN-SRV-DC01`
- Domain: `corp.northstar.local`

## Network Configuration

Connected the client to the VirtualBox Internal Network (`intnet`) and configured a static IP address.

| Setting | Value |
|---|---|
| IP address | `192.168.10.20` |
| Subnet mask | `255.255.255.0` |
| Default gateway | Not configured |
| Preferred DNS server | `192.168.10.10` |

## Setup and Verification

Installed Windows 11 Pro, completed the initial setup, and renamed the computer to `WIN-CLIENT01`.

Configured the client's IP address and DNS settings, then ran `ping 192.168.10.10` to test connectivity to the domain controller. Used `nslookup corp.northstar.local` to verify that the domain resolved through DNS.

Joined the client to the domain using the domain Administrator account. After Windows confirmed the domain join, the computer restarted.

Signed in with `CORP\Administrator` and ran `systeminfo` to verify that the computer belonged to `corp.northstar.local`.
