# Network Topology

## Purpose

This document defines the production network topology for the IoT camp lab so the UniFi Express 7 can be configured consistently across setup, camp delivery, and reset cycles.

## Design Goals

- Keep the lab isolated from campus production networks
- Make room-by-room addressing predictable for troubleshooting
- Separate instructor traffic from student and IoT device traffic
- Allow Home Assistant in each room to control that room's IoT devices
- Keep the design simple enough to deploy quickly on a portable UniFi gateway
- Stay within the UniFi Express 7 hardware limit of 4 SSIDs per radio band per AP

## Topology Summary

### Upstream Connectivity

- **WAN / upstream:** campus-provided internet-only handoff for the UniFi Express 7
- **Campus requirement:** no routing from the lab environment into institutional production networks
- **Lab edge device:** UniFi Express 7 provides routing, DHCP, VLANs, SSIDs, and firewall policy for the camp environment

### Internal Segments

The internal lab network is divided into:

- **Instructor network** for instructor laptop, administration, provisioning, and troubleshooting
- **Room 1 Trusted** for the Room 1 Raspberry Pi and trusted room devices
- **Room 1 IoT** for Room 1 Wi-Fi IoT endpoints
- **Room 2 Trusted** for the Room 2 Raspberry Pi and trusted room devices
- **Room 2 IoT** for Room 2 Wi-Fi IoT endpoints
- **Room 3 Trusted** for the Room 3 Raspberry Pi and trusted room devices
- **Room 3 IoT** for Room 3 Wi-Fi IoT endpoints
- **Room 4 Trusted** for the Room 4 Raspberry Pi and trusted room devices
- **Room 4 IoT** for Room 4 Wi-Fi IoT endpoints
- **Room 5 Trusted** for the Room 5 Raspberry Pi and trusted room devices
- **Room 5 IoT** for Room 5 Wi-Fi IoT endpoints
- **Room 6 Trusted** for instructor demo Raspberry Pi and trusted demo devices
- **Room 6 IoT** for instructor demo Wi-Fi IoT endpoints

Zigbee devices remain off Wi-Fi and connect through each room's USB coordinator attached to the Raspberry Pi.

## Wireless Design — Private Pre-Shared Keys (PPSK)

The UniFi Express 7 is limited to **4 SSIDs per radio band per AP**. A per-room SSID design (13 SSIDs) exceeds this limit. The solution is UniFi's native **Private Pre-Shared Key (PPSK)** feature.

### How PPSK Works

- A single SSID broadcasts for all rooms on a given segment type (Trusted or IoT).
- Each room is assigned its own unique passphrase under that SSID.
- When a device connects using Room 2's passphrase, UniFi automatically places it on VLAN 12.
- No RADIUS server is required — this is handled natively by the UX7.
- The result is full per-room VLAN isolation over only 3 total SSIDs.

### SSIDs (3 total)

- `IoT-Lab-Admin` — single passphrase, VLAN 10
- `IoT-Lab-Computers` — PPSK enabled, one passphrase per room mapped to that room's trusted VLAN
- `IoT-Lab-SmartHome` — PPSK enabled, one passphrase per room mapped to that room's IoT VLAN

### PPSK Key Mapping

> **All passphrases are stored in KeePass under the matching room entry. Redacted values are below**

| SSID | PPSK Name | Assigned VLAN | Passphrase | Used By |
|------|-----------|---------------|------------|---------|
| `IoT-Lab-Admin` | Instructor | 10 | `stored in keepass` | Instructor laptop, admin devices |
| `IoT-Lab-Computers` | R1-Trusted | 11 | `stored in keepass` | Room 1 Raspberry Pi and trusted devices |
| `IoT-Lab-Computers` | R2-Trusted | 12 | `stored in keepass` | Room 2 Raspberry Pi and trusted devices |
| `IoT-Lab-Computers` | R3-Trusted | 13 | `stored in keepass` | Room 3 Raspberry Pi and trusted devices |
| `IoT-Lab-Computers` | R4-Trusted | 14 | `stored in keepass` | Room 4 Raspberry Pi and trusted devices |
| `IoT-Lab-Computers` | R5-Trusted | 15 | `stored in keepass` | Room 5 Raspberry Pi and trusted devices |
| `IoT-Lab-Computers` | R6-Trusted | 16 | `stored in keepass` | Room 6 demo Raspberry Pi and trusted devices |
| `IoT-Lab-SmartHome` | R1-IoT | 21 | `stored in keepass` | Room 1 smart plugs, bulbs, Wi-Fi IoT devices |
| `IoT-Lab-SmartHome` | R2-IoT | 22 | `stored in keepass` | Room 2 smart plugs, bulbs, Wi-Fi IoT devices |
| `IoT-Lab-SmartHome` | R3-IoT | 23 | `stored in keepass` | Room 3 smart plugs, bulbs, Wi-Fi IoT devices |
| `IoT-Lab-SmartHome` | R4-IoT | 24 | `stored in keepass` | Room 4 smart plugs, bulbs, Wi-Fi IoT devices |
| `IoT-Lab-SmartHome` | R5-IoT | 25 | `stored in keepass` | Room 5 smart plugs, bulbs, Wi-Fi IoT devices |
| `IoT-Lab-SmartHome` | R6-IoT | 26 | `Ostored in keepass` | Room 6 demo smart plugs, bulbs, Wi-Fi IoT devices |

> **Note:** PPSK compatibility tested with Kasa and TP-Link smart devices prior before camp day. PPSK uses standard WPA2-Personal under the hood, so most devices work, but it is worth confirming if adding new devices.

## Standard Naming

### UniFi Network Names (one per VLAN, 13 total)

- `Instructor`
- `Room1-Trusted`, `Room2-Trusted`, `Room3-Trusted`, `Room4-Trusted`, `Room5-Trusted`, `Room6-Trusted`
- `Room1-IoT`, `Room2-IoT`, `Room3-IoT`, `Room4-IoT`, `Room5-IoT`, `Room6-IoT`

### UniFi Wi-Fi Networks (SSIDs, 3 total)

- `IoT-Lab-Admin`
- `IoT-Lab-Computers`
- `IoT-Lab-SmartHome`

## VLAN and IP Addressing Scheme

Use a single predictable private addressing block:

- **Supernet:** `10.50.0.0/16`

Each VLAN gets its own `/24`, which keeps setup simple and leaves room for growth.

| Network | VLAN | Subnet | Gateway | DHCP Scope | Notes |
|--------|------|--------|---------|------------|-------|
| Instructor | 10 | 10.50.10.0/24 | 10.50.10.1 | 10.50.10.100-10.50.10.199 | Instructor laptop, admin access, setup tools |
| Room1-Trusted | 11 | 10.50.11.0/24 | 10.50.11.1 | 10.50.11.100-10.50.11.199 | Pi, Home Assistant host, trusted room devices |
| Room2-Trusted | 12 | 10.50.12.0/24 | 10.50.12.1 | 10.50.12.100-10.50.12.199 | Pi, Home Assistant host, trusted room devices |
| Room3-Trusted | 13 | 10.50.13.0/24 | 10.50.13.1 | 10.50.13.100-10.50.13.199 | Pi, Home Assistant host, trusted room devices |
| Room4-Trusted | 14 | 10.50.14.0/24 | 10.50.14.1 | 10.50.14.100-10.50.14.199 | Pi, Home Assistant host, trusted room devices |
| Room5-Trusted | 15 | 10.50.15.0/24 | 10.50.15.1 | 10.50.15.100-10.50.15.199 | Pi, Home Assistant host, trusted room devices |
| Room6-Trusted | 16 | 10.50.16.0/24 | 10.50.16.1 | 10.50.16.100-10.50.16.199 | Instructor demo Pi, Home Assistant host, trusted demo devices |
| Room1-IoT | 21 | 10.50.21.0/24 | 10.50.21.1 | 10.50.21.100-10.50.21.199 | Wi-Fi IoT devices only |
| Room2-IoT | 22 | 10.50.22.0/24 | 10.50.22.1 | 10.50.22.100-10.50.22.199 | Wi-Fi IoT devices only |
| Room3-IoT | 23 | 10.50.23.0/24 | 10.50.23.1 | 10.50.23.100-10.50.23.199 | Wi-Fi IoT devices only |
| Room4-IoT | 24 | 10.50.24.0/24 | 10.50.24.1 | 10.50.24.100-10.50.24.199 | Wi-Fi IoT devices only |
| Room5-IoT | 25 | 10.50.25.0/24 | 10.50.25.1 | 10.50.25.100-10.50.25.199 | Wi-Fi IoT devices only |
| Room6-IoT | 26 | 10.50.26.0/24 | 10.50.26.1 | 10.50.26.100-10.50.26.199 | Instructor demo Wi-Fi IoT devices only |

### Why This Scheme Works

- VLAN IDs match the third octet of the subnet, which makes troubleshooting fast
- Trusted room networks stay in the `10.50.11.0/24` through `10.50.16.0/24` range
- IoT room networks stay in the `10.50.21.0/24` through `10.50.26.0/24` range
- The instructor network is easy to remember as VLAN `10` on `10.50.10.0/24`
- Every room keeps a consistent pairing: trusted VLAN = `10 + room`, IoT VLAN = `20 + room`

## Address Reservations

Keep low addresses reserved for infrastructure and known lab devices.

| Network | Recommended Static / Reserved Addresses |
|--------|------------------------------------------|
| Instructor | `.1` gateway, `.2-.19` infrastructure, `.20-.49` reserved admin devices |
| Each Room Trusted | `.1` gateway, `.10` Raspberry Pi / Home Assistant, `.11-.19` future trusted devices |
| Each Room IoT | `.1` gateway, `.10-.49` reserved smart devices if fixed DHCP mappings are desired |

Example:

- Room 1 Raspberry Pi / Home Assistant host: `10.50.11.10`
- Room 2 Raspberry Pi / Home Assistant host: `10.50.12.10`
- Room 3 Raspberry Pi / Home Assistant host: `10.50.13.10`
- Room 4 Raspberry Pi / Home Assistant host: `10.50.14.10`
- Room 5 Raspberry Pi / Home Assistant host: `10.50.15.10`
- Room 6 instructor demo Raspberry Pi / Home Assistant host: `10.50.16.10`

This makes each room's Pi easy to find without looking up leases.

## Wireless Mapping

With PPSK, VLAN assignment is driven by the passphrase used at connect time, not by SSID. The three SSIDs broadcast across all rooms.

| SSID | PPSK Key | VLAN | Subnet | Passphrase |
|------|----------|------|--------|------------|
| `IoT-Lab-Admin` | Instructor | 10 | 10.50.10.0/24 | `stored in keepass` |
| `IoT-Lab-Computers` | R1-Trusted | 11 | 10.50.11.0/24 | `stored in keepass` |
| `IoT-Lab-Computers` | R2-Trusted | 12 | 10.50.12.0/24 | `stored in keepass` |
| `IoT-Lab-Computers` | R3-Trusted | 13 | 10.50.13.0/24 | `stored in keepass` |
| `IoT-Lab-Computers` | R4-Trusted | 14 | 10.50.14.0/24 | `stored in keepass` |
| `IoT-Lab-Computers` | R5-Trusted | 15 | 10.50.15.0/24 | `stored in keepass` |
| `IoT-Lab-Computers` | R6-Trusted | 16 | 10.50.16.0/24 | `stored in keepass` |
| `IoT-Lab-SmartHome` | R1-IoT | 21 | 10.50.21.0/24 | `stored in keepass` |
| `IoT-Lab-SmartHome` | R2-IoT | 22 | 10.50.22.0/24 | `stored in keepass` |
| `IoT-Lab-SmartHome` | R3-IoT | 23 | 10.50.23.0/24 | `stored in keepass` |
| `IoT-Lab-SmartHome` | R4-IoT | 24 | 10.50.24.0/24 | `stored in keepass` |
| `IoT-Lab-SmartHome` | R5-IoT | 25 | 10.50.25.0/24 | `stored in keepass` |
| `IoT-Lab-SmartHome` | R6-IoT | 26 | 10.50.26.0/24 | `stored in keepass` |

## Traffic Policy

### Zone-Based Firewall Design

The UniFi zone-based firewall model groups networks into zones and applies policy between zones rather than writing per-network rules. This is the recommended approach for the UX7.

#### Zone Definitions

| Zone | Included VLANs / Networks | Purpose |
|------|--------------------------|---------|
| `WAN` | Upstream internet | Created automatically by UniFi |
| `Instructor` | VLAN 10 | Admin and management — highest trust |
| `Trusted` | VLANs 11–16 | All room Raspberry Pi / Home Assistant networks |
| `IoT` | VLANs 21–26 | All room smart device networks |

#### Zone Policy Matrix

| From → To | Action | Reason |
|-----------|--------|--------|
| `Instructor` → `Trusted` | Allow | Admin access to all room Pis |
| `Instructor` → `IoT` | Allow | Troubleshooting access to IoT devices |
| `Instructor` → `WAN` | Allow | Normal internet access |
| `Trusted` → `IoT` | Allow | Home Assistant controls smart devices |
| `Trusted` → `WAN` | Allow | Pi needs internet for Home Assistant setup and updates |
| `Trusted` → `Instructor` | Block | Rooms should not reach admin network |
| `IoT` → `Trusted` | Block | Devices cannot initiate back to Pis |
| `IoT` → `Instructor` | Block | Devices cannot reach admin network |
| `IoT` → `WAN` | Block* | Keeps IoT devices local-only |
| `WAN` → any | Block | Default deny all inbound |

> **\* IoT → WAN:** Block is the secure default. Allow temporarily if a specific device requires cloud onboarding, then re-block once the device is provisioned.

#### Cross-Room Consideration

Zone rules are zone-wide, so `Trusted → IoT: Allow` means any room's Pi can technically reach any room's IoT devices. For a one-day camp this is acceptable — students cannot access each other's Home Assistant dashboards, and the cross-room IoT risk is low.

If strict per-room enforcement is needed, add a **Traffic Rule** on top of the zone policy to block cross-room Trusted → IoT traffic. For example, block `10.50.11.0/24` from reaching `10.50.22.0/24` through `10.50.26.0/24`, and repeat for each room.

## UniFi Build Order

1. Configure the WAN uplink to the campus-provided internet-only handoff.
2. Create the 13 UniFi networks: `Instructor` (VLAN 10), `Room1-Trusted` through `Room6-Trusted` (VLANs 11–16), and `Room1-IoT` through `Room6-IoT` (VLANs 21–26).
3. Create the `IoT-Lab-Admin` Wi-Fi network with a single WPA2 passphrase mapped to VLAN 10.
4. Create the `IoT-Lab-Computers` Wi-Fi network with PPSK enabled. Add one private key per room (R1-Trusted through R6-Trusted), each mapped to its corresponding VLAN (11–16).
5. Create the `IoT-Lab-SmartHome` Wi-Fi network with PPSK enabled. Add one private key per room (R1-IoT through R6-IoT), each mapped to its corresponding VLAN (21–26).
6. Confirm all three SSIDs are broadcasting and that the per-key VLAN assignments are correct.
7. Apply DHCP reservations for each Raspberry Pi once its MAC address is known.
8. Add firewall rules to permit trusted-to-same-room-IoT control and block unwanted lateral movement.
9. Validate each room by connecting the Pi using the correct room key and confirming it receives an address on the expected subnet.
10. Test IoT device PPSK compatibility (Kasa/TP-Link) before camp day.

## Operational Notes

- PPSK is configured per Wi-Fi network in UniFi under **Settings → WiFi → [Network] → Private Pre-Shared Keys**.
- Each room's Pi and IoT devices should be provisioned with their room-specific key before onboarding so they land on the correct VLAN automatically.
- Store all PPSK values in KeePass under the matching room entry — do not store them in plaintext files.
- Record every SSID, VLAN, subnet, gateway, and PPSK value in KeePass after configuration.
- Label each Raspberry Pi and IoT device physically with room number before onboarding.
- If a device does not support PPSK, fall back to manually assigning it a fixed DHCP address and confirming its VLAN placement in UniFi client details.

## Recommended Final Standard

This should be treated as the implementation standard for the current 6-room portable lab:

- **Instructor/Admin:** VLAN `10`, subnet `10.50.10.0/24`, SSID `IoT-Lab-Admin` (single PSK)
- **Room Trusted VLANs:** `11-16`, subnets `10.50.11.0/24` through `10.50.16.0/24`, SSID `IoT-Lab-Computers` (PPSK)
- **Room IoT VLANs:** `21-26`, subnets `10.50.21.0/24` through `10.50.26.0/24`, SSID `IoT-Lab-SmartHome` (PPSK)
- **Total SSIDs:** 3 — within the UX7 4-per-band hardware limit
- **Total UniFi networks:** 13 — one per VLAN

Room 6 is reserved as the instructor demo environment. Demo equipment follows the same trusted/IoT pattern as the student rooms without sharing the admin-only instructor network.