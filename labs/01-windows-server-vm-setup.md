# Lab 01: Windows Server VM Setup

## Overview

Set up a Windows Server 2022 virtual machine in Oracle VirtualBox to serve as the foundation for the Active Directory lab environment.

## Environment

- Host OS: Windows 11 Home
- Virtualization: Oracle VirtualBox
- Server OS: Windows Server 2022 Standard Evaluation, Desktop Experience
- Computer name: `WIN-SRV-DC01`
- Memory: 8 GB
- Processors: 4
- Virtual disk: 80 GB

## Network Configuration

Configured two network adapters to provide internet access and private communication between lab virtual machines.

| Adapter | Network Mode | IPv4 Address | Purpose |
|---|---|---|---|
| Ethernet | NAT | `10.0.2.15` | Internet access through VirtualBox |
| Ethernet 2 | Internal Network (`intnet`) | `192.168.10.10` | Communication between lab virtual machines |

The internal adapter uses subnet mask `255.255.255.0` and has no default gateway.

## Setup and Verification

Installed Windows Server 2022 using the Custom installation option, configured the local Administrator account, and renamed the server to `WIN-SRV-DC01`.

Ran `DISM /online /Get-CurrentEdition` to verify the installed edition. The command returned `ServerStandardEval`, identifying the installation as Windows Server Standard Evaluation.

Installed Windows updates before proceeding with Active Directory configuration.
