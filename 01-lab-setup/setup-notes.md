# Lab Setup Notes

**Date built:** 2026-11-20
**Purpose:** Isolated environment for practising network scanning (Nmap), traffic analysis (Wireshark) and later web security and SIEM work.

## Environment

| Item | Value |
|---|---|
| Host OS | Windows |
| Hypervisor | Oracle VirtualBox |
| Attacker VM | Kali Linux, Nmap 7.99 |
| Target VM | Metasploitable 2 (Ubuntu 8.04 based, 32-bit) |
| Host-only network | `192.168.56.0/24` |
| Kali resources | RAM: 3 GB |
| Metasploitable resources | RAM: 1 GB |

## Build steps

1. **Created a host-only network** in VirtualBox Network Manager: `192.168.56.1/24`, DHCP server enabled.
2. **Downloaded Metasploitable 2** from the official project page and extracted the `.vmdk` disk image.
3. **Created the Metasploitable VM:** type Linux, version Ubuntu (32-bit), attached the existing `.vmdk` as its disk, and set Adapter 1 to Host-only.
4. **Booted Metasploitable** and logged in with its default credentials. Confirmed the IP with `ifconfig`: `192.168.56.103`.
5. **Added a second adapter to Kali:** Adapter 1 stays NAT (updates), Adapter 2 set to Host-only on the same network. Kali's lab interface `eth1` received `192.168.56.102`.
6. **Tested connectivity** with `ping`, `nmap -sn` and a service scan (see below).
7. **Checked DVWA** at `http://192.168.56.103/dvwa` using its default login, and set the security level to Low for practice.
8. **Took snapshots** of both VMs once everything worked.

## Verification evidence

Host discovery from Kali (`nmap -sn 192.168.56.0/24`) found 4 hosts up out of 256 addresses:

| IP | Identified as | Evidence |
|---|---|---|
| 192.168.56.1 | Windows host | Host-only gateway address |
| 192.168.56.100 | VirtualBox DHCP server | VirtualBox virtual NIC, `.100` is the default DHCP address |
| 192.168.56.102 | Kali (scanning machine) | No MAC shown, which is normal for the local machine |
| 192.168.56.103 | Metasploitable 2 | VirtualBox virtual NIC, matches `ifconfig` on the target |

Packet capture on `eth1` showed the expected behaviour for a SYN scan: SYN probes from Kali, RST-ACK replies from closed ports, SYN-ACK from open ports (for example 139), and a RST from Kali to cancel the half-open connection.

## Snapshots

| VM | Snapshot name | Taken when |
|---|---|---|
| Metasploitable 2 | `clean-baseline` | After first successful boot and connectivity test |
| Kali | `kali-baseline-lab-ready` | After the second adapter was working |

To reset a VM: power it off, open its Snapshots tab, right-click the snapshot and choose Restore.

## Safety rules

- Metasploitable stays on the host-only network only. Never bridged, never NAT, no port forwarding.
- Scan only `192.168.56.103` and, lightly, `scanme.nmap.org`.
- No credentials, tokens or real-world data are stored in this repository.
