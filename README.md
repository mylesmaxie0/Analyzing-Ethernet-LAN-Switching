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

### ICMP Frame Analysis
This phase demonstrates how traffic flows after MAC address resolution has completed. Once the switch knows the destination MAC, traffic is forwarded as a known unicast.

This validates:
- Deterministic frame forwarding
- Proper CAM table usage
- Elimination of unnecessary flooding

#### Expected Behavior
1. ICMP Echo Request sent from PC1 to PC2.
2. Frame is forwarded only to the port associated with PC2’s MAC.
3. No broadcast flooding occurs.
4. Echo Reply follows the same unicast path back.

#### Wireshark Capture 
<img width="1725" height="971" alt="Screenshot 2026-02-11 at 4 08 04 PM" src="https://github.com/user-attachments/assets/26b5784f-3f6a-4874-8e6d-e2f4b2f845ed" />

You can now observe that ICMP traffic between PC1 and PC4 is being forwarded as known unicast frames. Because SW1 previously learned both MAC addresses during the ARP exchange, the switch is able to reference its CAM table and forward each ICMP Echo Request and Echo Reply directly to the appropriate port. No flooding occurs during this phase.

#

### Switch MAC Address Table Verification
This phase confirms that the switch dynamically learned MAC addresses from source fields of incoming frames and correctly associated them with physical ports.

This validates:
- MAC learning logic
- Port-to-MAC mapping
- Dynamic entry behavior
- Aging timer function

#### Switch MAC Address Table 
<img width="938" height="289" alt="Screenshot 2026-02-11 at 4 14 21 PM" src="https://github.com/user-attachments/assets/bca910e9-725e-4671-ba5d-4b0dbcbf485c" />

#### Expected Output Behavior
- Each active host MAC address appears in the table
- Entries are marked as “Dynamic.”
- Each MAC is mapped to the correct switch port
- Entries will age out after inactivity (typically 300 seconds = 5 Minutes)

#### Key Insight

The switch does not know MAC addresses beforehand. It builds its forwarding intelligence entirely from observed traffic.

#

### Why These Phases Matter

Together, these phases prove:
1. ARP enables Layer 3 communication.
2. Switches flood only when necessary.
3. Once learned, unicast forwarding is precise.
4. Ethernet switching is entirely MAC-driven.

#

### Conclusion

This lab demonstrated how Ethernet LAN switching works in a real environment using Wireshark and switch verification. By analyzing ARP and ICMP traffic, we observed how the switch dynamically learns MAC addresses, populates its CAM table, and transitions from broadcast discovery to efficient unicast forwarding.

The packet captures confirmed that ARP enables initial communication, and once MAC addresses are learned, the switch forwards traffic directly without flooding. Overall, this project reinforced core Layer 2 switching concepts through practical analysis and validation.
