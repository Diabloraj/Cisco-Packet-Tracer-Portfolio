# 🏥 Enterprise Network Architecture: Zero-Trust Segmentation in Healthcare

## 📌 Project Overview
This project simulates a mission-critical, HIPAA-compliant enterprise healthcare network architecture using **Cisco Packet Tracer**. To safeguard sensitive patient medical records and prevent unauthorized horizontal intrusion, the infrastructure implements **Zero-Trust Network Segmentation** using **Extended Access Control Lists (ACLs)** and strict **VLAN isolation**.

End-to-end data connectivity between the hospital campus LAN and the off-site **Central EMR (Electronic Medical Record) Cloud Database** (`200.100.50.0/24`) is powered by multi-area **OSPFv2 Dynamic Routing** over a dedicated point-to-point WAN link.

---

## 🏗️ Architectural & Security Highlights
* **Zero-Trust L3 Segmentation (Extended ACL 110):** Applied strict inbound filtering on the Patient Medical Record Server sub-interface (`Gig0/0.10`) to explicitly block lateral communication attempts originating from restricted staff stations (`VLAN 20`) and untrusted public zones (`VLAN 30`).
* **HIPAA Compliance Zones:** Structured broadcast domains into functional security tiers: **Patient Medical Records (VLAN 10)**, **Doctor/Nursing Stations (VLAN 20)**, and **Patient WiFi Zone (VLAN 30)**.
* **Router-on-a-Stick (ROAS) Routing:** Configured IEEE 802.1Q VLAN encapsulation across gateway sub-interfaces (`Gig0/0.10`, `.20`, `.30`) on the primary edge router (`Router0`) to handle controlled Inter-VLAN routing.
* **Dynamic WAN Routing (OSPFv2):** Deployed Single-Area OSPF (Area 0) across campus edge routers (`Router0` and `Router1`) over a `10.0.0.0/24` WAN backbone to ensure fast convergence and deterministic route propagation to the EMR Cloud Database.

---

## 🧮 VLAN & IP Addressing Plan

| VLAN ID | Medical Department / Zone | Network Subnet | Gateway (ROAS Sub-interface) | Security & Access Rule |
| :--- | :--- | :--- | :--- | :--- |
| **VLAN 10** | **Patient Medical Records Server** | `192.168.10.0/24` | `192.168.10.1` (Gig0/0.10) | **Strict Inbound ACL 110 Applied** |
| **VLAN 20** | **Doctor / Nursing Station** | `192.168.20.0/24` | `192.168.20.1` (Gig0/0.20) | Isolated Medical Staff Zone |
| **VLAN 30** | **Patient WiFi Zone (Untrusted)** | `192.168.30.0/24` | `192.168.30.1` (Gig0/0.30) | Public Internet / Guest Access Only |
| **WAN Link** | **Inter-Router P2P Link** | `10.0.0.0/24` | R0: `10.0.0.1` / R1: `10.0.0.2` | OSPF Backbone (Area 0) |
| **Cloud Zone** | **Central EMR Cloud Database** | `200.100.50.0/24` | `200.100.50.1` (Gig0/0 on R1) | Secure Cloud Storage Gateway |

---

## 🛡️ Zero-Trust Security Enforcement (ACL 110 Audit)
The following Extended Access Control List was deployed on `Router0` to enforce strict isolation for the Medical Record Server:

```text
! Deny inbound sessions from Doctor Stations (VLAN 20) to Medical Records (VLAN 10)
access-list 110 deny ip 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255

! Deny inbound sessions from untrusted Patient WiFi (VLAN 30) to Medical Records (VLAN 10)
access-list 110 deny ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255

! Permit required outgoing synchronization traffic to the Central EMR Cloud Database
access-list 110 permit ip 192.168.10.0 0.0.0.255 any
