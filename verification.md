## ✅ Verification Checklist - 

## 📑 Table of Contents

1. [🔍 Verify VLANS](#-verify-vlans)
2. [🔗 Verify Trunks](#-verify-trunks)
3. [🚦 Verify Router Subinterfaces](#-verify-router-subinterfaces) 
4. [📡 Verify DHCP Assignment](#-verify-dhcp-assignment)
5. [🔌 Verify Inter-VLAN Connectivity](#-verify-inter-vlan-connectivity)
6. [📡 Test DHCP Failover](#-test-dhcp-failover)
7. [🌐 Verify Connectivity](#-verify-connectivity)
8. [🔍 Test Inter VLAN after Failover](#-test-inter-vlan-after-failover)

---

## 🔍 Verify VLANS

```text
SW1> show vlan brief
SW2> show vlan brief
```

- Screenshots: `screenshots/sw1_vlan_brief.png`,`screenshots/sw2_vlan_brief.png`
---

## 🔗 Verify Trunks
```text
SW1> show interfaces trunk
SW2> show interfaces trunk
```

- Screenshots: `screenshots/sw1_trunk_status.png`,`screenshots/sw2_trunk_status.png`
---

## 🚦 Verify Router Subinterfaces

```text
R1> show ip interface brief
```
- Screenshots: `screenshots/r1_ip_brief.png`
---

## 📡 Verify DHCP Assignment

> Note: 
- PCs are intended to receive IPs dynamically from DHCP. 
- Due to Packet Tracer limitation with multi-VLAN DHCP (router-on-a-stick), DHCP may fail to assign the correct IP, gateway, or DNS. 
- Manual IPs within the DHCP pool were used for verification.

| PC  | IP Address     | Subnet Mask    | Default Gateway |
|-----|----------------|----------------|---------------- |
| PC1 | 192.168.10.50  | 255.255.255.0  | 192.168.10.1    |
| PC3 | 192.168.20.50  | 255.255.255.0  | 192.168.20.1    |

- Screenshots: `screenshots/pc1_vlan10_ip_assigned.png`, `screenshots/pc3_vlan20_ip_assigned.png`
---

## 🔌 Verify Inter-VLAN Connectivity

```text
PC1> ping 192.168.20.50
PC3> ping 192.168.10.50
```

- Screenshots: `screenshots/pc1_ping_vlan20.png`, `screenshots/pc3_ping_vlan10.png`
---

## 📡 Test DHCP Failover

- Turn Server1 OFF, Server2 ON.
- Assign backup IPs manually (Packet Tracer limitation)
```text
PC1: 192.168.10.101
PC3: 192.168.20.101
```

- Screenshots: `screenshots/pc1_failover_ip.png`, `screenshots/pc3_failover_ip.png`
---

## 🌐 Verify Connectivity
```text
PC1> ping 192.168.10.1
PC3> ping 192.168.20.1
```

- Screenshots: `screenshots/pc1_ping_failover_gw.png`, `screenshots/pc3_ping_failover_gw.png`
---

## 🔍 Test Inter VLAN after Failover

```text
PC1> ping 192.168.20.101
PC3> ping 192.168.10.101
```

- Screenshots: `screenshots/pc1_ping_vlan20_failover.png`, `screenshots/pc3_ping_vlan10_failover.png`

---
