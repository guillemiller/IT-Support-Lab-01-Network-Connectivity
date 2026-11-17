# IT Support Lab 01 – Network Connectivity Troubleshooting

## Overview

This project documents a simulated IT support incident involving a Windows workstation that lost network connectivity after being moved to a different desk.

The goal was to identify the root cause, restore Ethernet connectivity, obtain a valid network configuration and verify communication with the default gateway.

**Environment:** JoeLearns Labs – simulated environment  
**Incident ID:** INC-1043  
**Workstation:** ACCT-WS12  
**Operating system:** Windows 11

## 1. Incident Description

A desktop workstation in the Accounting department could not access internal network resources or the Internet after a desk relocation.

The user's laptop continued working over Wi-Fi, but the desktop workstation had no Ethernet connectivity.

## 2. Investigation

### Step 1 – Physical connectivity

Inspected the workstation's Ethernet connection and checked the NIC link status.

- The network adapter was enabled.
- No link/activity LEDs were detected.
- The Ethernet cable was connected to the workstation and wall outlet.

Further investigation revealed that the C-12 wall connection was not patched to switch SW-ACCESS-02, port Gi1/0/12.

### Step 2 – Switch port investigation

Reviewed the simulated switch interface status.

- Interface: GigabitEthernet1/0/12
- Status: down/down
- Description: ACCT-C12
- Access VLAN: 20

The port configuration was consistent with the workstation's assigned network, but the physical connection was missing.

### Step 3 – Root cause identification

The relocation checklist had not included the patch-panel connection for desk C-12.

**Root cause:** Missing patch cable between IDF panel C-12 and switch port Gi1/0/12.

## 3. Resolution

1. Connected patch-panel port C-12 to SW-ACCESS-02 Gi1/0/12.
2. Confirmed that the physical link was restored.
3. Renewed the workstation's DHCP lease.
4. Tested connectivity to the default gateway using ICMP.

## 4. Network Configuration

| Parameter | Result |
|---|---|
| IP address | 192.168.20.84 |
| Subnet mask | 255.255.255.0 (/24) |
| Default gateway | 192.168.20.1 |
| DNS server | 192.168.20.10 |
| VLAN | 20 |

## 5. Connectivity Validation

Executed:

`ping 192.168.20.1`

Result:
- Packets sent: 4
- Packets received: 4
- Packets lost: 0
- Response time: less than 1 ms

The successful ping confirmed communication between the workstation and its default gateway.

**Note:** This test does not independently confirm Internet access or connectivity to every internal resource.

## 6. Skills Practiced

- Windows network troubleshooting
- Ethernet physical connectivity
- Switch interface status interpretation
- VLAN fundamentals
- IPv4 addressing and subnet masks
- DHCP lease renewal
- Default gateway configuration
- ICMP and ping testing
- Root cause analysis
- Incident documentation

## 7. Key Takeaway

This exercise reinforced the importance of following a structured troubleshooting process: gathering evidence, identifying the root cause, applying the minimum necessary change and validating the result before closing an incident.

**Disclaimer:** This is a simulated training exercise completed in JoeLearns Labs. It does not represent production network administration experience.
