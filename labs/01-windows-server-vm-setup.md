# Lab 01: Windows Server VM Setup

## Overview

Set up a Windows Server 2022 virtual machine in VirtualBox as the foundation for an Active Directory lab environment.

## Environment

- Host: Windows 11 Home
- Virtualization: Oracle VirtualBox
- Operating system: Windows Server 2022 Standard Evaluation, Desktop Experience
- Computer name: `WIN-SRV-DC01`
- Memory: 8 GB
- Processors: 4
- Virtual disk: 80 GB
- Network: NAT

## Setup and Verification

Windows Server was installed using the custom installation option. The local Administrator account was configured, and the computer was renamed to `WIN-SRV-DC01`.

The installed edition was checked using `DISM /online /Get-CurrentEdition`, which returned `ServerStandardEval`. Windows also reports an activated status.

The server currently receives its IPv4 address through DHCP, with IPv6 enabled. NAT provides the initial network connection while the server is being prepared for the lab.

## Next Steps

Review the current network configuration and prepare a stable IP address for the server before installing Active Directory Domain Services.
