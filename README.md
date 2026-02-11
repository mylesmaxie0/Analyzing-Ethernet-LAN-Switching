# Analyzing Ethernet LAN Switching

## Project Overview
This lab demonstrates practical Layer 2 Ethernet switching behavior and packet analysis using Wireshark. The objective is to validate MAC address learning, broadcast flooding, ARP resolution, and unicast frame forwarding through direct packet inspection.

This lab reinforces foundational networking concepts aligned with CCNA-level switching theory and provides hands-on protocol analysis.

# 

## Lab Objectives
- Analyze Ethernet II frame structure
- Observe switch MAC address learning behavior
- Capture and interpret ARP request/reply traffic
- Validate broadcast vs. unicast forwarding
- Inspect ICMP echo traffic at Layer 2 and Layer 3

#

## Network Topology
<img width="1687" height="1003" alt="Screenshot 2026-02-11 at 3 01 17 PM" src="https://github.com/user-attachments/assets/4f9f0cc4-c362-4329-b74e-9a7d5b1d56c7" />

#

## Technologies Used
- Ethernet (IEEE 802.3)
- ARP (RFC 826)
- IPv4
- ICMP
- Wireshark
- Managed Layer 2 Switch (or virtual equivalent)

#

## Quick Breakdown on Ethernet LAN Switching

#### How it Works
1. A frame enters a switch port
2. The switch reads the source MAC address and stores it in the MAC (CAM) table.
3. The switch checks the destination MAC address:
- Known unicast -> Forward to specific port
- Unknown unicast -> Flood
- Broadcast -> Flood to all ports except ingress

#### Key Concepts 
- Each switch port is its own collision domain.
- All ports share the same broadcast domain (unless VLANs are configured).
- MAC table entries are learned dynamically and age out after 5 minutes if an endpoint is inactive.
- ARP is required before IPv4 communication can occur.

#
### ARP Resolution Analysis
This phase demonstrates how hosts discover each other’s MAC addresses before Layer 3 communication can occur. Since switches forward based on MAC addresses (not IP addresses), ARP is required to map an IP address to a physical MAC address.

This phase validates:
- Broadcast flooding behavior
- Initial MAC learning process
- Transition from broadcast to unicast communication

#### Checking SW1 MAC-Address Table


