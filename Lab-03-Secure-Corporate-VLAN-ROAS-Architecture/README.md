# 🔒 Secure Corporate Network Infrastructure for Startups (VLAN Segmentation & ROAS)

## 📌 Project Overview
This project simulates a production-grade, highly secure internal local area network (LAN) infrastructure designed for a scalable corporate startup using **Cisco Packet Tracer**. The architecture enforces strict broadcast domain segmentation across multiple departments using **Virtual Local Area Networks (VLANs)** and enables controlled inter-VLAN communication via a **Router-on-a-Stick (ROAS)** topology.

To ensure centralized and automated IP addressing across isolated broadcast domains, the network implements a **DHCP Relay Agent (`ip helper-address`)** architecture directing client requests to a centralized DHCP Server in a protected Server Zone.

---

## 🏗️ Architectural & Security Highlights
* **VLAN Segmentation:** Divided the corporate LAN into four functional zones: **Management (VLAN 10)**, **Developer (VLAN 20)**, **Guest (VLAN 30)**, and the **Server Zone (VLAN 40)** to shrink broadcast domains and isolate critical infrastructure.
* **Router-on-a-Stick (ROAS):** Configured IEEE **802.1Q trunking encapsulation** on sub-interfaces (`GigabitEthernet0/0.10`, `.20`, `.30`, `.40`) on the primary gateway router to handle routing between isolated VLANs over a single physical link.
* **Centralized DHCP Relay Services:** Configured `ip helper-address` across all user-facing sub-interfaces to forward broadcast DHCP discovery packets as unicast traffic to the centralized DHCP server (`192.168.40.10`) residing in VLAN 40.
* **Switch Trunking & Access Mapping:** Configured explicit IEEE 802.1Q trunk ports between the switch and router, while assigning dedicated user end-devices to specific access VLANs.

---

## 🧮 VLAN Schema & IP Addressing Plan

| VLAN ID | Zone / Department | Network Subnet | Gateway (ROAS Sub-interface) | Centralized DHCP Helper |
| :--- | :--- | :--- | :--- | :--- |
| **VLAN 10** | **Management Zone** | `192.168.10.0/24` | `192.168.10.1` (Gig0/0.10) | `192.168.40.10` |
| **VLAN 20** | **Developer Zone** | `192.168.20.0/24` | `192.168.20.1` (Gig0/0.20) | `192.168.40.10` |
| **VLAN 30** | **Guest Zone** | `192.168.30.0/24` | `192.168.30.1` (Gig0/0.30) | `192.168.40.10` |
| **VLAN 40** | **Server Zone** | `192.168.40.0/24` | `192.168.40.1` (Gig0/0.40) | *N/A (Static IP Assignment)* |

---

## 🔍 NOC Verification & Troubleshooting Commands
To audit VLAN assignments, 802.1Q trunking status, and DHCP relay operations, the following CLI commands were utilized:

```text
! Verify active VLAN database and switchport access assignments
Switch# show vlan brief

! Inspect active 802.1Q trunk ports and allowed VLANs
Switch# show interfaces trunk

! Verify Router-on-a-Stick (ROAS) sub-interfaces and IP bindings
Router# show ip interface brief

! Inspect routing table for directly connected VLAN subnets
Router# show ip route connected
