# Project 5: DHCP Redundancy with Failover in Office Network

## Table of Contents
1. [Project Overview](#project-overview)
2. [Project Objective](#project-objective)
3. [Lab Topology](#lab-topology)
4. [Device Interface Table](#device-interface-table)
5. [IP Addressing Table](#ip-addressing-table)
6. [Lab Steps](#lab-steps)
7. [Device Configuration](#device-configuration)
   - [Switch 1](#switch-1)  
   - [Switch 2](#switch-2)  
   - [Router 1](#router-1)  
   - [Server 1 DHCP POOL](#server-1-dhcp-pool)  
   - [Server 2 DHCP POOL](#server-2-dhcp-pool)   
8. [Packet Tracer Limitations & Workarounds](#packet-tracer-limitations-workarounds)
9. [Verification](#verification)
10. [Folder Structure](#folder-structure)

---

## 1️⃣ Project Overview

This lab demonstrates DHCP redundancy in a small office network. Two DHCP servers provide IP addresses to PCs across VLANs. 
If the primary DHCP server fails, the backup server ensures continued IP availability. 
The lab also demonstrates router-on-a-stick configuration for inter-VLAN communication.

> Note: 
Due to Packet Tracer simulation limitations, DHCP may not function correctly in multi-VLAN setups with router-on-a-stick. 
Manual IP assignment was used for demonstration purposes to verify the lab objectives, including inter-VLAN communication and DHCP failover.

---

## 2️⃣ Project Objective

- Configure DHCP pools on a primary server for VLAN10 and VLAN20.
- Configure a backup DHCP server to provide failover.
- Verify inter-VLAN communication using a router-on-a-stick.
- Demonstrate DHCP failover scenario.
- Document Packet Tracer limitations and provide workarounds.

---

## 3️⃣ Lab Topology

- **Devices:** 2 DHCP Servers, 1 Router (R1), 2 Switches (SW1, SW2), 6 PCs  
- **VLAN Assignment:**  
  - VLAN10: PC1, PC2, PC5  
  - VLAN20: PC3, PC4, PC6  

**Draw.io Topology File:**  

---

## 4️⃣ Device Interface Table

| Device   | Interface | Connected Device| Connected Interface| VLAN / Notes                 |
|----------|-----------|-----------------|-------------------|-------------------------------|
| Router   | G0/0      | Switch 1        | Fa0/24            | Trunk / VLAN 10 & 20          |
| Switch1  | Fa0/1     | PC1             | NIC               | VLAN10                        |
| Switch1  | Fa0/2     | PC2             | NIC               | VLAN10                        |
| Switch1  | Fa0/3     | PC3             | NIC               | VLAN20                        |
| Switch1  | Fa0/4     | DHCP Server1    | NIC               | VLAN10                        |
| Switch1  | Fa0/5     | DHCP Server2    | NIC               | VLAN10 (backup)               |
| Switch1  | Fa0/23    | Switch2         | Fa0/23            | Trunk / inter-switch link     |
| Switch1  | Fa0/24    | Router G0/0     | G0/0              | Trunk / VLAN 10 & 20          |
| Switch2  | Fa0/1     | PC4             | NIC               | VLAN20                        |
| Switch2  | Fa0/2     | PC5             | NIC               | VLAN10                        |
| Switch2  | Fa0/3     | PC6             | NIC               | VLAN20                        |
| Switch2  | Fa0/23    | Switch1         | Fa0/23            | Trunk / inter-switch link     |

---

## 5️⃣ IP Addressing Table

| Device / VLAN | IP Address (Planned DHCP Pool) | Subnet Mask     | Default Gateway| Notes / Verification Workaround         |
|---------------|--------------------------------|-----------------|----------------|-----------------------------------------|
| PC1 (VLAN10)  | 192.168.10.50–100 (DHCP)       | 255.255.255.0   | 192.168.10.1   | Manual IP assigned due to PT limitation |
| PC2 (VLAN10)  | 192.168.10.50–100 (DHCP)       | 255.255.255.0   | 192.168.10.1   | Manual IP assigned for verification     |
| PC5 (VLAN10)  | 192.168.10.50–100 (DHCP)       | 255.255.255.0   | 192.168.10.1   | Manual IP assigned for verification     |
| PC3 (VLAN20)  | 192.168.20.50–100 (DHCP)       | 255.255.255.0   | 192.168.20.1   | Manual IP assigned for verification     |
| PC4 (VLAN20)  | 192.168.20.50–100 (DHCP)       | 255.255.255.0   | 192.168.20.1   | Manual IP assigned for verification     |
| PC6 (VLAN20)  | 192.168.20.50–100 (DHCP)       | 255.255.255.0   | 192.168.20.1   | Manual IP assigned for verification     |
| Router G0/0.10| 192.168.10.1                   | 255.255.255.0   | -              | VLAN10 SVI for DHCP gateway             |
| Router G0/0.20| 192.168.20.1                   | 255.255.255.0   | -              | VLAN20 SVI for DHCP gateway             |
| Server1 VLAN10| 192.168.10.10                  | 255.255.255.0   | 192.168.10.1   | Primary DHCP server                     |
| Server2 VLAN10| 192.168.10.11                  | 255.255.255.0   | 192.168.10.1   | Backup DHCP server, used for failover   |

> Note: Manual IP assignment was used for verification due to Packet Tracer DHCP limitations.

---

## 6️⃣ Lab Steps

1. Configure VLANs on SW1 and SW2.  
2. Configure router-on-a-stick (R1 subinterfaces) for VLAN10 & VLAN20.  
3. Configure primary DHCP server with pools for VLAN10 and VLAN20.  
4. Configure backup DHCP server with pools for VLAN10 and VLAN20 (keep OFF initially).  
5. Connect PCs to respective VLAN ports.  
6. Assign trunk links between switches and router.  
7. Verify inter-VLAN routing and DHCP assignment (manual IP assignment if PT fails).  
8. Simulate failover: turn off Server1, turn on Server2, assign backup IPs, test connectivity.  

---

## 7️⃣ Device Configuration

### Switch 1 
```text
enable
configure terminal
```
! Set hostname
```
hostname SW1
```
! Disable DNS lookup to avoid delays
```text
no ip domain-lookup
```
! Create VLANs
```text
vlan 10
name HR
exit
vlan 20
name IT
exit
```
! Assign access ports
```text
interface fa0/1
switchport mode access
switchport access vlan 10
description PC1
exit

interface fa0/2
switchport mode access
switchport access vlan 10
description PC2
exit

interface fa0/3
switchport mode access
switchport access vlan 20
description PC3
exit

interface fa0/4
switchport mode access
switchport access vlan 10
description DHCP_Server1
exit

interface fa0/5
switchport mode access
switchport access vlan 10
description DHCP_Server2_Backup
exit
```
! Inter-switch trunk link
```text
interface fa0/23
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 10,20
description Trunk_to_SW2
exit
```
! Router trunk link
```text
interface fa0/24
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 10,20
description Trunk_to_Router
exit
```
! Enable trunk ports and access ports
```text
interface range fa0/1 - 5 , fa0/23 - 24
no shutdown
exit
```
! Save configuration
```text
end
write memory
```
---
### Switch 2
```text
enable
configure terminal
```
! Hostname
```text
hostname SW2
```
! Avoid DNS lookup
```text
no ip domain-lookup
```
! Create VLANs
```text
vlan 10
name HR
exit
vlan 20
name IT
exit
```
! Access ports for PCs
```text
interface fa0/1
description PC4
switchport mode access
switchport access vlan 20
no shutdown
exit

interface fa0/2
description PC5
switchport mode access
switchport access vlan 10
no shutdown
exit

interface fa0/3
description PC6
switchport mode access
switchport access vlan 20
no shutdown
exit
```
! Inter-switch trunk to SW1
```text
interface fa0/23
description Trunk_to_SW1
switchport mode trunk
switchport trunk allowed vlan 10,20
no shutdown
exit
```
! Router trunk link on SW2 (to Router G0/1)
```text
interface fa0/24
description Trunk_to_Router_G0/1
switchport mode trunk
switchport trunk allowed vlan 10,20
no shutdown
exit
```
```text
end
write memory
```
---
### Router 1
```text
enable
configure terminal

hostname R1
```
! Ensure physical interface up
```text
interface GigabitEthernet0/0
no shutdown
exit
```
! Subinterface for VLAN 10
```text
interface GigabitEthernet0/0.10
description VLAN10 - HR
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
no shutdown
exit
```
! Subinterface for VLAN 20
```text
interface GigabitEthernet0/0.20
description VLAN20 - IT
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
no shutdown
exit
```
! DHCP relay (forward DHCP/BOOTP requests to both primary and backup servers)
! Primary DHCP server IP: 192.168.10.10
! Backup DHCP server IP: 192.168.10.11
```text
interface GigabitEthernet0/0.20
ip helper-address 192.168.10.10
ip helper-address 192.168.10.11
exit
```
! Optional: set a hostname and disable DNS lookup (avoids delays)
```text
no ip domain-lookup

end
write memory
```
---
### Server 1 DHCP POOL

**Primary Server**

**Desktop → IP Configuration**

- IP Address: 192.168.10.10
- Subnet Mask: 255.255.255. 0
- Default Gateway: 192.168.10.1

**Configure DHCP pools (Packet Tracer Server GUI)**

**Create Pool VLAN10-HR**
- Default Gateway: 192.168.10.1
- Start IP: 192.168.10.50
- End IP: 192.168.10.100
- Subnet Mask: 255.255.255.0

**Create Pool VLAN20-FIN**

- Default Gateway: 192.168.20.1
- Start IP: 192.168.20.50
- End IP: 192.168.20.100
- Subnet Mask: 255.255.255.0

---
### Server 2 DHCP POOL

**Backup Server**

**Desktop → IP Configuration**

- IP Address: 192.168.10.11
- Subnet Mask: 255.255.255.0
- Default Gateway: 192.168.10.1

**Configure DHCP pools (Packet Tracer Server GUI)**

**Create Pool VLAN10-HR-BACKUP**

- Default Gateway: 192.168.10.1
- Start IP: 192.168.10.101
- End IP: 192.168.10.150
- Subnet Mask: 255.255.255.0

**Create Pool VLAN20-FIN-BACKUP**

- Default Gateway: 192.168.20.1
- Start IP: 192.168.20.101
- End IP: 192.168.20.150
- Subnet Mask: 255.255.255.0

---

## 8️⃣ Packet Tracer Limitations & Workarounds

- DHCP in multi-VLAN topologies may:  
  1. Assign IP outside the DHCP pool  
  2. Fail to assign default gateway and DNS  

**Summary:** 

The DHCP issue occurs because Router-on-a-Stick with multiple VLANs in Packet Tracer cannot reliably forward DHCP requests/responses. 
Manual IP assignment was used to complete verification steps and demonstrate failover.

---
## 9️⃣ Verification

### Topology Overview

<img src="topology\topology_overview.png" alt="LAB TOPOLOGY" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">


## Step 1: Verify VLANs
```text
SW1> show vlan brief
SW2> show vlan brief
```
**SW1**
<img src="screenshots\sw1_vlan_brief.png" alt="SW1 VLAN BRIEF" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**SW2**
<img src="screenshots\sw2_vlan_brief.png" alt="SW2 VLAN BRIEF" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

## Step 2: Verify Trunks
```text
SW1> show interfaces trunk
SW2> show interfaces trunk
```
**SW1**
<img src="screenshots\sw1_trunk_status.png" alt="SW1 TRUNK STATUS" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**SW2**
<img src="screenshots\sw2_trunk_status.png" alt="SW2 TRUNK STATUS" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

## Step 3: Verify Router Subinterfaces
```text
R1> show ip interface brief
```
**R1** 
<img src="screenshots\r1_ip_brief.png" alt="R1 IP INTERFACE BRIEF" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

## Step 4: Verify DHCP Assignment (Manual Workaround)

> Note: 
- PCs are intended to receive IPs dynamically from DHCP. 
- Due to Packet Tracer limitation with multi-VLAN DHCP (router-on-a-stick), DHCP may fail to assign the correct IP, gateway, or DNS. 
- Manual IPs within the DHCP pool were used for verification.

| PC  | IP Address     | Subnet Mask    | Default Gateway |
|-----|----------------|----------------|---------------- |
| PC1 | 192.168.10.50  | 255.255.255.0  | 192.168.10.1    |
| PC3 | 192.168.20.50  | 255.255.255.0  | 192.168.20.1    |

**PC1**
<img src="screenshots\pc1_vlan10_ip_assigned.png" alt="PC1 Static IP VLAN 10" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**PC3**
<img src="screenshots\pc3_vlan20_ip_assigned.png" alt="PC3 Static IP VLAN 20" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

## Step 5: Verify Inter-VLAN Connectivity
```text
PC1> ping 192.168.20.50
PC3> ping 192.168.10.50
```

**PC1**
<img src="screenshots\pc1_ping_vlan20.png" alt="PC1 PING VLAN 20" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**PC3**
<img src="screenshots\pc3_ping_vlan10.png" alt="PC3 PING VLAN 10" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

## Step 6: Test DHCP Failover

- Turn Server1 OFF, Server2 ON.
- Assign backup IPs manually (Packet Tracer limitation)
```text
PC1: 192.168.10.101
PC3: 192.168.20.101
```

**PC1**
<img src="screenshots\pc1_failover_ip.png" alt="PC1 PING FAILOVER" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**PC3**
<img src="screenshots\pc3_failover_ip.png" alt="PC3 PING FAILOVER" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

- Verify connectivity to gateway:
```text
PC1> ping 192.168.10.1
PC3> ping 192.168.20.1
```

**PC1**
<img src="screenshots\pc1_ping_failover_gw.png" alt="PC1 PING FAILOVER GATEWAY" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**PC3**
<img src="screenshots\pc3_ping_failover_gw.png" alt="PC3 PING FAILOVER GATEWAY" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

## Step 7: Test Inter-VLAN after Failover
```text
PC1> ping 192.168.20.101
PC3> ping 192.168.10.101
```
**PC1**
<img src="screenshots\pc1_ping_vlan20_failover.png" alt="PC1 PING FAILOVER VLAN20" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**PC2**
<img src="screenshots\pc3_ping_vlan10_failover.png" alt="PC1 PING FAILOVER VLAN10" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

---
## Folder Structure

dhcp-redundancy-with-Failover-in-Office-Network/
├─ README.md
├─ LICENSE
├─ verification.md
├─ lab_files/
│   └─ dhcp-redundancy-with-Failover-in-Office-Network.pkt
├─ topology/
│   └─ topology_overview.png
├─ router-configs/
│   └─ R1.cfg
├─ switch-configs/
│   ├─ SW1.cfg
│   └─ SW2.cfg
├─ server-configs/
│   ├─ server1.cfg
│   └─ server2.cfg
├─ screenshots/
│   ├─ pc1_failover_ip.png
│   ├─ pc1_ping_failover_gw.png
│   ├─ pc1_ping_vlan20.png
│   ├─ pc1_ping_vlan20_failover.png
│   ├─ pc1_vlan10_ip_assigned.png
│   ├─ pc3_failover_ip.png
│   ├─ pc3_ping_failover_gw.png
│   ├─ pc3_ping_vlan10.png
│   ├─ pc3_ping_vlan10_failover.png
│   ├─ r1_ip_brief.png
│   ├─ sw1_trunk_status.png
│   ├─ sw1_vlan_brief.png
│   ├─ sw2_trunk_status.png
│   └─ sw2_vlan_brief.png
|


---
