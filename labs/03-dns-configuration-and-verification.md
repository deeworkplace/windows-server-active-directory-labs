# Lab 03: DNS Configuration and Verification

## Overview

Configured DNS for the `corp.northstar.local` domain and investigated an incorrect DNS record that caused the domain to resolve to both the NAT and internal network addresses.

## DNS Configuration

- DNS server: `WIN-SRV-DC01`
- Domain: `corp.northstar.local`
- Forward lookup zone: `corp.northstar.local`
- Internal IP address: `192.168.10.10`
- Subnet mask: `255.255.255.0`

## Troubleshooting and Verification

Ran `nslookup corp.northstar.local` and found that the domain resolved to both `10.0.2.15` (NAT) and `192.168.10.10` (internal network).

Disabled DNS registration on the NAT adapter to prevent its IP address from being registered in DNS. In DNS Manager, deleted the root Host (A) record pointing to `10.0.2.15` and preserved the records for `192.168.10.10`.

Ran `ipconfig /flushdns` to clear the local DNS cache and `ipconfig /registerdns` to request DNS registration. Afterward, `nslookup corp.northstar.local` returned only `192.168.10.10`.

Also ran `dcdiag /test:dns`. The diagnostic reported warnings, including one related to dynamic IP addressing. The DNS lookup was corrected, but the diagnostic warnings were not all resolved.
