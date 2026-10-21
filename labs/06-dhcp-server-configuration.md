# Lab 06: DHCP Server Configuration

## Overview

Set up DHCP on `WIN-SRV-DC01` to assign IP addresses automatically to devices on the internal lab network.

## Configuration

- DHCP server: `WIN-SRV-DC01`
- Domain: `corp.northstar.local`
- Network: VirtualBox Internal Network (`intnet`)
- Scope name: `NorthStar Internal Network`
- IP address range: `192.168.10.100–192.168.10.200`
- Subnet mask: `255.255.255.0`
- DNS server: `192.168.10.10`
- Lease duration: 8 days

## Setup and Testing

Installed the DHCP Server role on `WIN-SRV-DC01` using Server Manager. Completed the post-installation steps to create the DHCP security groups and authorize the server in Active Directory.

Created and activated the `NorthStar Internal Network` scope, using `192.168.10.100–192.168.10.200` as the address range. Set `192.168.10.10` as the DNS server and left the default gateway blank because the internal lab network has no router.

Changed `WIN-CLIENT01` to obtain its IP address and DNS settings automatically. Ran `ipconfig /all` and confirmed that the client received `192.168.10.100`, with `192.168.10.10` listed as both the DHCP server and DNS server.
