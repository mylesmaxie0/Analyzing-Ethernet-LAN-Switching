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
<img width="861" height="393" alt="Screenshot 2026-02-11 at 3 24 02 PM" src="https://github.com/user-attachments/assets/2ac92b2d-3bff-4ce8-89ac-75ee6090d0f1" />

As seen, SW1's MAC Address table is empty, which will change once PC1 tries to reach PC4.

#### PC1 Ping Request to PC4
<img width="861" height="231" alt="Screenshot 2026-02-11 at 3 31 02 PM" src="https://github.com/user-attachments/assets/1ec57ee7-f692-4062-bf46-6ca3a493ad4c" />

#### Expected Behavior 
1. PC1 does not know PC2’s MAC address.
2. PC1 sends an ARP Request as a broadcast (ff:ff:ff:ff:ff:ff).
3. The switch floods the frame out all ports except the ingress port.
4. PC2 responds with a unicast ARP Reply.
5. The switch learns both MAC addresses and updates its CAM table.

#### Wireshark Capture 
<img width="1725" height="804" alt="Screenshot 2026-02-11 at 3 35 11 PM" src="https://github.com/user-attachments/assets/6cfb2ca8-4fa7-41d1-a0e1-064e5e743403" />

In the packet capture, frames 7 and 8 illustrate the completion of the ARP resolution process.

Frame 7 shows PC1 broadcasting an ARP Request to determine which host owns the IP address 192.168.100.4. Because PC1 does not yet know the corresponding MAC address, the frame is sent to the broadcast address (ff:ff:ff:ff:ff:ff), causing the switch to flood the request out all ports except the ingress port.

Frame 8 contains the ARP Reply from PC4. In this response, PC4 provides its hardware (MAC) address directly to PC1 via unicast transmission. Upon receiving these frames, SW1 dynamically learns the source MAC addresses of both PC1 and PC4 and records them in its CAM table. This allows the switch to make precise forwarding decisions for subsequent traffic between the two hosts.

#

#### SW1's updated MAC-Address Table
<img width="905" height="188" alt="Screenshot 2026-02-11 at 3 47 36 PM" src="https://github.com/user-attachments/assets/06e6bfb1-7be1-4b31-89f2-e6fdd5a0b3b9" />

You can now observe that SW1’s CAM table contains dynamically learned entries for both PC1 and PC4. The switch has associated each MAC address with the corresponding ingress port from which the frames were received. This confirms that SW1 successfully performed MAC address learning during the ARP exchange and can now forward subsequent unicast traffic between the two hosts without flooding.

