# MAC Address and ARP

## MAC Address

MAC stands for Media Access Control.

A MAC address is a Layer 2 (Data Link Layer) address used for local network communication and Ethernet frame delivery.

Example:

00:1A:2B:3C:4D:5E

MAC addresses are traditionally 48 bits long.


00:1A:2B:3C:4D:5E

A MAC address contains 6 bytes:

00 → 1 byte
1A → 1 byte
2B → 1 byte
3C → 1 byte
4D → 1 byte
5E → 1 byte

Therefore:

6 bytes × 8 bits = 48 bits
IPv4 vs MAC Address
IP Address
→ Layer 3
→ Logical addressing
→ Routing

MAC Address
→ Layer 2
→ Local delivery
→ Frame

Simple rule:

IP tells where the packet needs to go, while MAC helps deliver the frame on the current local link.

MAC Address and Hexadecimal

MAC addresses are normally written using hexadecimal notation because representing 48 bits directly in binary would be difficult to read.

Example:

00:1A:2B:3C:4D:5E

Each hexadecimal digit represents 4 bits.

OUI

The first 24 bits of a traditionally globally administered MAC address represent the OUI (Organizationally Unique Identifier).

Example:

00:1A:2B | 3C:4D:5E
   OUI    | Interface Identifier

The OUI is associated with an organization/vendor allocation.

Modern systems can also use locally administered or randomized MAC addresses, especially for Wi-Fi privacy, so a MAC prefix should not always be assumed to identify the physical manufacturer.

ARP

ARP stands for Address Resolution Protocol.

ARP is used to discover the MAC address associated with a known IPv4 address on the local network.

Example:

Known:
Router IP = 192.168.1.1

Unknown:
Router MAC = ?

ARP helps discover:

192.168.1.1
      ↓
ARP
      ↓
BB:BB:BB:BB:BB:BB
ARP Request

Suppose a laptop knows the router's IP address:

192.168.1.1

but does not know its MAC address.

The laptop sends an ARP Request:

"Who has 192.168.1.1?"

The ARP Request is normally sent as a Layer 2 broadcast.

Laptop
   ↓
ARP Request → Broadcast
   ↓
All devices on the local LAN

Only the device that owns the requested IP should respond.

ARP Reply

The device that owns the requested IP sends an ARP Reply.

Example:

ARP Request:
"Who has 192.168.1.1?"

ARP Reply:
"192.168.1.1 is at BB:BB:BB:BB:BB:BB"

Normally:

ARP Request → Broadcast
ARP Reply   → Unicast
ARP Cache

Operating systems temporarily store recently learned IP-to-MAC mappings in an ARP cache.

Example:

IP Address       MAC Address

192.168.1.1      BB:BB:BB:BB:BB:BB
192.168.1.50     CC:CC:CC:CC:CC:CC

Windows:

arp -a

Linux:

ip neigh
ARP Cache Flow
Need MAC address
      ↓
Check ARP Cache
      ↓
Entry exists?
   ↙       ↘
 Yes       No
  ↓         ↓
Use MAC   ARP Request

If the required mapping already exists in the cache, the device normally does not need to send a new ARP Request immediately.

Same Subnet vs Different Subnet

This is one of the most important ARP rules.

Same Subnet

Example:

Laptop:
192.168.1.74/24

Destination:
192.168.1.50

Both devices are on the same subnet.

If the destination MAC is unknown:

ARP for destination IP
Laptop
192.168.1.74
     ↓
ARP Request
"Who has 192.168.1.50?"
     ↓
192.168.1.50

The laptop needs the destination device's MAC.

Different Subnet

Example:

Laptop:
192.168.1.74/24

Destination:
192.168.2.50

Default Gateway:
192.168.1.1

The destination is on a different network.

The laptop does NOT directly ARP for:

192.168.2.50

Instead, it needs the MAC address of the default gateway:

192.168.1.1

So:

Destination IP
192.168.2.50
      ↓
Different Network
      ↓
Default Gateway
192.168.1.1
      ↓
ARP
      ↓
Gateway MAC
Core Rule
Same subnet
→ ARP for destination MAC

Different subnet
→ ARP for gateway MAC
IP vs MAC During Routing

Suppose:

Laptop → Router 1 → Router 2 → Router 3 → Google

MAC addresses are used for the current Layer 2 hop.

Example:

Laptop → Router 1

Source MAC:
Laptop MAC

Destination MAC:
Router 1 MAC

Router 1 forwards the packet to Router 2 using a new Layer 2 frame:

Router 1 → Router 2

Source MAC:
Router 1 MAC

Destination MAC:
Router 2 MAC

The same process continues for each Layer 2 link.

Important Rule

MAC addresses change hop-by-hop.

The IP packet is routed across the path:

Laptop → R1 → R2 → R3 → Google

The source and destination IP addresses normally remain the same during routing, although fields such as TTL change and NAT can modify addresses in networks using NAT.

Packet vs Frame

At Layer 3:

IP Packet

At Layer 2:

Frame

A packet is encapsulated inside a frame before being transmitted over a Layer 2 link.

Frame
┌───────────────────────────────┐
│ Source MAC                    │
│ Destination MAC               │
│                               │
│       IP Packet               │
│ Source IP                     │
│ Destination IP                │
│                               │
└───────────────────────────────┘

Therefore:

MAC → Current Layer 2 hop
IP  → Layer 3 destination
Gratuitous ARP

Gratuitous ARP (GARP) is used by a device to announce or update its own IP-to-MAC mapping on the local network.

Unlike a normal ARP Request, the device is not simply asking:

"Who has this IP?"

Instead, it is effectively announcing:

"My IP is associated with this MAC."

Example:

Device:

IP  = 192.168.1.74
MAC = AA:AA:AA:AA:AA:AA

It can announce the mapping:

192.168.1.74 → AA:AA:AA:AA:AA:AA
Uses of Gratuitous ARP

GARP can be useful for:

Duplicate IP detection
Updating ARP cache information
High-availability/failover environments
Network interface changes
Failover Example

Suppose:

Server A
IP  = 192.168.1.10
MAC = AA:AA:AA:AA:AA:AA

Server A fails.

Server B takes over the same IP:

Server B
IP  = 192.168.1.10
MAC = BB:BB:BB:BB:BB:BB

Server B can send a Gratuitous ARP announcement:

192.168.1.10 → BB:BB:BB:BB:BB:BB

This helps relevant systems update their ARP information.

ARP Spoofing / ARP Poisoning

ARP does not provide strong authentication.

An attacker can attempt to send malicious ARP information and manipulate the victim's IP-to-MAC mapping.

Normal mapping:

192.168.1.1
      ↓
Router MAC

Potentially poisoned mapping:

192.168.1.1
      ↓
Attacker MAC

The victim may then send traffic intended for the gateway toward the attacker.

Depending on the attack and network configuration, this can enable Man-in-the-Middle (MITM) activity or traffic disruption.

ARP Spoofing Example

Normal network:

Victim
192.168.1.74
    ↓
Router
192.168.1.1

Attacker:

Attacker
192.168.1.100

The attacker attempts to convince the victim:

192.168.1.1 → Attacker MAC

instead of:

192.168.1.1 → Router MAC

The victim's ARP cache may then contain an incorrect mapping.

SOC Detection Perspective

A SOC analyst should investigate suspicious IP-to-MAC changes.

Example:

192.168.1.1 → AA:AA:AA:AA:AA:AA

Later:

192.168.1.1 → DD:DD:DD:DD:DD:DD

The same IP is now associated with a different MAC.

This can be a strong indicator of ARP spoofing, but it does not automatically prove an attack.

Possible legitimate causes include:

Router replacement
Failover
Network interface changes
Virtualization
MAC randomization
Network reconfiguration
SOC Investigation Flow
Same IP
   ↓
Different MAC
   ↓
Investigate
   ↓
Check ARP traffic
   ↓
Identify source MAC/device
   ↓
Check whether change is legitimate
   ↓
Confirm or rule out ARP spoofing
Wireshark ARP Analysis

Wireshark display filter:

arp

Normal ARP traffic may look like:

ARP Request
Who has 192.168.1.1?

ARP Reply
192.168.1.1 is at BB:BB:BB:BB:BB:BB

Suspicious example:

192.168.1.1 is at BB:BB:BB:BB:BB:BB

followed by:

192.168.1.1 is at DD:DD:DD:DD:DD:DD

Repeated unexpected changes should be investigated for possible ARP spoofing/poisoning.

Complete ARP Flow
Need destination MAC
        ↓
Check ARP Cache
        ↓
Mapping available?
    ↙          ↘
  Yes           No
   ↓             ↓
Use MAC      ARP Request
                 ↓
             Broadcast
                 ↓
          Device with IP
                 ↓
            ARP Reply
                 ↓
              Unicast
                 ↓
            Learn MAC
                 ↓
             Build Frame
Key Rules
ARP
→ IPv4 address to MAC address resolution
→ Local network only
ARP Request
→ Usually Broadcast
ARP Reply
→ Usually Unicast
Same subnet
→ ARP for destination MAC
Different subnet
→ ARP for default gateway MAC
ARP Cache
→ Temporarily stores IP-to-MAC mappings
GARP
→ Announces/updates own IP-to-MAC mapping
ARP Spoofing
→ Attacker attempts to manipulate IP-to-MAC mappings
MAC
→ Layer 2
→ Current-hop delivery
IP
→ Layer 3
→ Logical addressing and routing
MAC + ARP Summary
MAC addresses operate at Layer 2.
Traditional Ethernet MAC addresses are 48 bits.
MAC addresses are normally written in hexadecimal.
ARP resolves a known IPv4 address to a MAC address on the local network.
ARP Requests are normally broadcast.
ARP Replies are normally unicast.
ARP cache stores recently learned IP-to-MAC mappings.
Same-subnet communication requires the destination MAC.
Different-subnet communication requires the default gateway's MAC for the first local hop.
Routers create a new Layer 2 frame at each hop.
MAC addresses change hop-by-hop.
IP addresses normally remain end-to-end during routing.
Gratuitous ARP can announce or update an IP-to-MAC mapping.
Unexpected IP-to-MAC changes can be an indicator of ARP spoofing.
ARP spoofing should be investigated rather than automatically treated as a confirmed attack.
