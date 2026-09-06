# Routing

## 1. What is Routing?

Routing is the process of selecting a path or next hop for forwarding an IP packet toward its destination.

A router uses a routing table to decide where a packet should be sent.

Basic flow:

Packet arrives
→ Read Destination IP
→ Check Routing Table
→ Find matching route
→ Select best route
→ Forward packet


## 2. Role of a Router

A router works at Layer 3 (Network Layer).

Its main job is to forward IP packets between different networks.

Example:

Kali → Router → Internet

The router checks the destination IP and decides where the packet should go next.


## 3. Routing Table

A routing table contains routes that help a device determine the appropriate path, next hop, and outgoing interface for a destination.

Example:


default via 10.227.100.117 dev eth0
10.227.100.0/24 dev eth0 

The routing table can be thought of as a map that tells the system where to send packets.

4. Destination Network

A destination network is the network that contains the destination IP.

Example:

10.227.100.0/24

This represents the local network in the current Kali environment.

5. Next Hop / Gateway

The next hop is the next Layer 3 device to which the packet should be sent.

Example:

default via 10.227.100.117

Here:

10.227.100.117 = Gateway / Next Hop

For an Internet destination such as 8.8.8.8, Kali sends the packet first to 10.227.100.117.

6. Outgoing Interface

The outgoing interface is the network interface through which the packet leaves the system.

Example:

dev eth0

This means the packet is sent through the eth0 interface.

7. Directly Connected Network

A directly connected network is a network that can be reached directly through a local interface without using a gateway.

Example:

10.227.100.0/24 dev eth0

This means the 10.227.100.0/24 network is directly connected through eth0.

Therefore, a destination such as:

10.227.100.50

can be reached directly through eth0.

8. Default Route

A default route is used when no more-specific route matches the destination.

The default route is written as:

0.0.0.0/0

Example:

default via 10.227.100.117 dev eth0

Meaning:

If there is no specific route for the destination, send the packet through 10.227.100.117 using eth0.

Example:

Destination: 8.8.8.8

8.8.8.8
   ↓
No specific local route
   ↓
Default Route
   ↓
10.227.100.117
   ↓
eth0
9. Routing vs ARP

Routing and ARP perform different jobs.

Routing

Routing decides:

Where should the packet go next?

ARP

ARP finds:

What MAC address belongs to the next hop IP?

Example:

Destination IP: 8.8.8.8
        ↓
Routing Table
        ↓
Next Hop: 10.227.100.117
        ↓
ARP / Neighbor Resolution
        ↓
Find MAC address of 10.227.100.117
        ↓
Create Ethernet Frame

Important rule:

Routing decides the next hop. ARP finds the next hop's MAC address.

10. Current Kali Routing Table

Current routing table:

default via 10.227.100.117 dev eth0 proto dhcp src 10.227.100.170 metric 100
10.227.100.0/24 dev eth0 proto kernel scope link src 10.227.100.170 metric 100
First line
default via 10.227.100.117 dev eth0 proto dhcp src 10.227.100.170 metric 100

Meaning:

default → used when no more-specific route matches
via 10.227.100.117 → gateway / next hop
dev eth0 → outgoing interface
proto dhcp → route was learned/installed through DHCP configuration
src 10.227.100.170 → preferred source IP for this route
metric 100 → route preference/cost value
Second line
10.227.100.0/24 dev eth0 proto kernel scope link src 10.227.100.170 metric 100

Meaning:

10.227.100.0/24 → directly connected destination network
dev eth0 → reachable through eth0
proto kernel → route was automatically created by the kernel from interface/network configuration
scope link → destination is directly reachable on the local link
src 10.227.100.170 → preferred source IP
metric 100 → route preference/cost value
11. Longest Prefix Match

When multiple routes match the same destination, the most specific route is preferred.

This is called:

Longest Prefix Match (LPM)

Example:

10.0.0.0/8      → Router A
10.1.0.0/16     → Router B
10.1.1.0/24     → Router C

Destination:

10.1.1.50

All three routes can match, but:

/24 > /16 > /8

Therefore:

10.1.1.0/24

is the best match.

Important rule:

Multiple routes match → the most specific (longest prefix) route wins.

12. Default Route and Longest Prefix Match

The default route:

0.0.0.0/0

is extremely broad.

Therefore, a more-specific matching route is preferred over the default route.

Example:

10.0.0.0/8      → Router A
10.1.0.0/16     → Router B
10.1.1.0/24     → Router C
0.0.0.0/0       → Router D

For:

10.1.1.50

the /24 route is selected.

For:

8.8.8.8

the specific routes above do not match, so the default route is used.

13. Practical: ip route

Command:

ip route

This displays the Linux routing table.

Current result:

default via 10.227.100.117 dev eth0 proto dhcp src 10.227.100.170 metric 100
10.227.100.0/24 dev eth0 proto kernel scope link src 10.227.100.170 metric 100
14. Practical: ip route get

The command:

ip route get <destination>

asks Linux which route it would use for a specific destination.

Test 1
ip route get 8.8.8.8

Observed:

8.8.8.8 via 10.227.100.117 dev eth0 src 10.227.100.170

Interpretation:

Destination → 8.8.8.8
Next Hop    → 10.227.100.117
Interface   → eth0
Source IP   → 10.227.100.170

Because 8.8.8.8 is not inside the local 10.227.100.0/24 network, the default route is used.

Test 2
ip route get 10.227.100.50

Observed:

10.227.100.50 dev eth0 src 10.227.100.170

Interpretation:

Destination → 10.227.100.50
Interface   → eth0
Source IP   → 10.227.100.170

There is no via 10.227.100.117 because 10.227.100.50 is inside the directly connected 10.227.100.0/24 network.

15. Key Takeaways
Routing decides where an IP packet should go next.
The routing table contains routes used for forwarding decisions.
default is used when no more-specific route matches.
via identifies the next hop/gateway.
dev identifies the outgoing interface.
A directly connected network does not require a gateway.
ARP is used to find the MAC address of the local next hop.
Longest Prefix Match selects the most specific matching route.
ip route displays the routing table.
ip route get <destination> shows the route Linux would use for a destination.
