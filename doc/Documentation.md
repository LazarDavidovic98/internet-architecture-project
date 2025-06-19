# Internet Architecture Project – Documentation

## 1. Addressing Plan

### IPv4 Subnets
- VLAN Management: `192.168.0.0/24`
- VLAN Development: `55.8.0.0/24`
- VLAN Marketing: `55.8.1.0/24`
- LANs: LAN1-LAN12 with `/24`
- P2P Links: `/30` subnets (e.g., `55.8.14.0/30`)

### IPv6 Subnets (AS 200nn only)
- LAN6 to LAN10: `2001:33:EF08:X::/64`
- P2P Links (e.g., R6-R7): `2001:33:EF08:Y::/64`
- Link-local addresses for routers: `FE80::X`

## 2. Interface Configuration
All routers and switches are configured with assigned IPs (see [PDF](Report.pdf) for full table). Subinterfaces used for VLANs on R5.

## 3. VLAN Implementation
- VLAN IDs:
  - VLAN 10 → Development
  - VLAN 20 → Marketing
  - VLAN 99 → Management
- Switch SW1 and SW2:
  - Ports 1-3 for Dev, 4-6 for Marketing
  - Port 15 (SW2) for Admin via VLAN 99
  - Trunk on ports 23-24
- Inter-VLAN routing enabled on R5 via subinterfaces with dot1Q encapsulation

## 4. Static Routing (R4, R8)
- R4 (AS 100nn) uses static route to R1
- R8 (AS 200nn) uses IPv4 and IPv6 static routes to R7

## 5. OSPF Multi-area (AS 100nn)
- Area 0 (Backbone), Area 1, and Area 2 defined
- R4 excluded; routes redistributed using `redistribute static`
- Configured on R1–R3 and R5

## 6. EIGRP (AS 200nn)
- Dual-stack EIGRP for IPv4 and IPv6 on R6, R7, R9
- R8 excluded; its LAN routes redistributed via `redistribute static`

## 7. RIP (AS 300nn)
- RIPv2 configured on R10, R11, and R12
- Covers networks `55.8.12.0/24` and `55.8.13.0/24`

## 8. External BGP (AS 100nn, 200nn, 300nn ↔ ISP)
- BGP AS numbers: 10008, 20008, 30008, 40008
- R1, R6, and R10 establish neighbor relationships with ISP
- Weight attribute used to simulate primary/backup link selection

## 9. Route Redistribution
- Between BGP and internal protocols:
  - R1: `redistribute bgp` into OSPF
  - R6: `redistribute bgp` into EIGRP
  - R10: `default-information originate` into RIP

---

## 🔍 Validation & Testing
- Ensure `show ip route` shows redistributed routes
- Test connectivity across VLANs
- Verify static routing to R4/R8 LANs
- Confirm BGP neighbors via `show ip bgp summary`
