# IPv4 Addressing

IPv4 (Internet Protocol version 4) is a 32-bit logical addressing system used to identify network interfaces and enable communication between devices on an IP network.

## IPv4 Address Structure

An IPv4 address contains 32 bits divided into four 8-bit sections called octets.

Example:


192.168.1.74

The four octets are:

192 → 1st octet
168 → 2nd octet
1   → 3rd octet
74  → 4th octet

Each octet contains 8 bits.

8 + 8 + 8 + 8 = 32 bits
Binary Representation

IPv4 addresses are represented in dotted-decimal notation for humans, but internally they are represented in binary.

Example:

192.168.1.74

Binary representation:

11000000.10101000.00000001.01001010

Therefore:

IPv4 = 32 bits = 4 octets
Octet Range

Each octet contains 8 bits and can represent 256 possible values.

The range of each octet is:

0 - 255

Example:

192.168.1.74     → Valid IPv4 address
10.20.30.40      → Valid IPv4 address
192.168.1.300    → Invalid IPv4 address

An octet cannot contain a value greater than 255.

Network and Host Portions

An IPv4 address logically contains a network portion and a host portion.

Example:

192.168.1.74/24

In a /24 network, the first 24 bits represent the network portion and the remaining 8 bits represent the host portion.

Conceptually:

Network Portion       Host Portion
<---------------->    <-------->
192.168.1             74

Detailed subnetting and CIDR calculations will be covered separately.

Network Address

The network address identifies the network itself.

For example:

192.168.1.0/24

Here:

192.168.1.0 → Network Address

The network address is not normally assigned to an individual host.

Host Address

Host addresses are used to identify individual devices/interfaces within a network.

For example:

192.168.1.1
192.168.1.2
192.168.1.3
...
192.168.1.74
...
192.168.1.254

Example:

Laptop  → 192.168.1.74
Phone   → 192.168.1.75
Printer → 192.168.1.76
Broadcast Address

A broadcast address is used to communicate with all applicable hosts on a subnet.

For:

192.168.1.0/24

the broadcast address is:

192.168.1.255

Basic /24 structure:

192.168.1.0       → Network Address
192.168.1.1-254   → Host Addresses
192.168.1.255     → Broadcast Address

The broadcast address is not normally assigned to an individual host.

Private IPv4 Addresses

Private IPv4 addresses are used inside private networks and are not directly routable on the public Internet.

The three main private IPv4 ranges are:

10.0.0.0/8
172.16.0.0/12
192.168.0.0/16

Examples:

10.10.20.30       → Private
172.20.10.5       → Private
192.168.1.74      → Private

Important:

172.40.10.5       → Not a private IP

The private 172.x.x.x range is only:

172.16.0.0 - 172.31.255.255
Public vs Private IP

Private IP addresses are used inside local/private networks.

They are not directly routable across the public Internet.

Typical home network flow:

Laptop
192.168.1.74
     ↓
Home Router
     ↓
NAT
     ↓
Public IP
     ↓
Internet
     ↓
Web Server

NAT allows private addresses to communicate with Internet services through a public IP address.

Default Gateway

A default gateway is typically the router's local interface that provides a path from the local network to other networks.

Example:

Laptop
IP Address:      192.168.1.74
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.1.1

If the destination is on the same local network:

Laptop
192.168.1.74
     ↓
192.168.1.50

the laptop can communicate directly with the destination.

If the destination is on a different network:

Laptop
192.168.1.74
     ↓
Default Gateway
192.168.1.1
     ↓
Internet / Other Network

the packet is sent to the default gateway first.

Same Network vs Different Network

The subnet mask helps a device determine whether a destination is on the same local network or a different network.

Example:

IP Address:   192.168.1.74
Subnet Mask:  255.255.255.0

Destination:

192.168.1.50

This is on the same network:

192.168.1.0/24

Therefore, the device can communicate directly with the destination.

But:

192.168.2.50

is on a different network.

The device sends the packet to its default gateway.

IP Address and MAC Address

IP addresses provide logical addressing and help with routing.

MAC addresses are used for local Layer 2 delivery.

IP Address  → Logical addressing and routing
MAC Address → Local network communication

When the destination is on the same network, the device can use the destination device's MAC address for the local frame.

When the destination is on a different network, the device sends the frame to the default gateway's MAC address.

ARP in Local Delivery

When a device knows an IPv4 address but does not know the corresponding MAC address, it can use ARP to discover the MAC address on the local network.

Same network:

Destination IP
      ↓
ARP
      ↓
Destination MAC
      ↓
Frame

Different network:

Destination IP
      ↓
Different Network
      ↓
Default Gateway
      ↓
ARP
      ↓
Gateway MAC
      ↓
Frame
Key Concepts
IPv4 uses 32 bits.
IPv4 contains four 8-bit octets.
Each octet has a range of 0-255.
IPv4 provides logical addressing and supports routing.
Network addresses identify networks.
Host addresses identify individual devices/interfaces.
Broadcast addresses are used to reach all applicable hosts on a subnet.
Private IPv4 addresses are used inside private networks.
Private IP addresses are not directly routable on the public Internet.
NAT can translate private addresses for Internet communication.
The default gateway provides a path to other networks.
The subnet mask helps determine whether a destination is local or remote.
MAC addresses are used for local Layer 2 delivery.
ARP maps a known IPv4 address to a MAC address on the local network.

---

## Static vs Dynamic IP Addressing

### Static IP Address

A static IP address is a fixed IP address that is manually configured or assigned in a way that keeps the address consistent.

Static IP addresses are commonly useful for:

- Servers
- Network devices
- Printers
- CCTV/NVR systems
- Infrastructure devices

Example:

Web Server
IP Address: 192.168.1.10

The address remains predictable, making it easier for clients and services to reach the server.

Dynamic IP Address

A dynamic IP address is automatically assigned to a device, usually through DHCP.

Example:

Laptop
      ↓
DHCP
      ↓
192.168.1.74

A dynamic IP address can change when the DHCP lease changes.

Static vs Dynamic
Static IP  → Fixed / Configured
Dynamic IP → Automatically assigned / Can change

A typical home network may use a stable LAN address for the router while laptops and phones receive dynamic addresses through DHCP. However, both routers and devices can be configured differently depending on the network.

DHCP

DHCP (Dynamic Host Configuration Protocol) automatically provides network configuration to clients.

DHCP can provide:

IP Address
Subnet Mask
Default Gateway
DNS Server

Example:

IP Address      → 192.168.1.74
Subnet Mask     → 255.255.255.0
Default Gateway → 192.168.1.1
DNS Server      → 192.168.1.1
DHCP Ports

DHCP uses UDP:

DHCP Server → UDP 67
DHCP Client → UDP 68
DHCP DORA Process

The basic DHCP process is known as DORA:

D → Discover
O → Offer
R → Request
A → Acknowledgment
1. DHCP Discover

The client does not yet have a valid IP configuration and searches for a DHCP server.

Client → DHCP Discover → DHCP Server

The initial discovery is generally broadcast.

2. DHCP Offer

The DHCP server offers network configuration to the client.

Example:

IP Address      → 192.168.1.74
Subnet Mask     → 255.255.255.0
Default Gateway → 192.168.1.1
DNS Server      → 192.168.1.1
3. DHCP Request

The client requests the offered configuration.

Client → DHCP Request → DHCP Server
4. DHCP ACK

The DHCP server confirms the configuration.

DHCP Server → DHCP ACK → Client

The client can now use the assigned network configuration.

DHCP Lease and Renewal

A DHCP-assigned IP address is normally provided for a specific lease period rather than permanently.

Example:

IP Address: 192.168.1.74
Lease Time: 8 hours

Before the lease expires, the client can attempt to renew the lease.

DHCP Lease
    ↓
Renewal
    ↓
DHCP Server
    ↓
ACK
    ↓
Lease Continues
Loopback Address

The IPv4 loopback range is:

127.0.0.0/8

The most commonly used loopback address is:

127.0.0.1

Loopback is used to communicate with the same computer and test the local TCP/IP networking stack.

Example:

ping 127.0.0.1

The traffic does not need to travel through the external network or Internet.

Computer
   ↓
127.0.0.1
   ↓
Same Computer
APIPA

APIPA stands for Automatic Private IP Addressing.

The IPv4 APIPA/link-local range is:

169.254.0.0/16

If a Windows device cannot obtain a valid IPv4 configuration from DHCP, it may automatically assign itself an address from the 169.254.x.x range.

Example:

DHCP
  ↓
No valid DHCP response
  ↓
APIPA
  ↓
169.254.20.15

An APIPA address normally does not provide normal public Internet connectivity.

Troubleshooting Significance

If a Windows system has:

IPv4 Address      → 169.254.x.x
Default Gateway   → Blank

a DHCP or network configuration problem should be investigated.

Possible areas to investigate include:

DHCP server availability
Network connectivity
DHCP Discover/Offer traffic
DHCP service
DHCP relay
VLAN configuration
Firewall or network filtering
Unicast

Unicast is one-to-one communication.

One Sender → One Receiver

Example:

Laptop → Google Server

A normal HTTPS request from a laptop to a web server is typically unicast.

Broadcast

Broadcast is one-to-all communication within a broadcast domain.

One Sender → All Applicable Hosts

ARP Request is an important example of Layer 2 broadcast communication.

Example:

Laptop
   ↓
ARP Request
"Who has 192.168.1.1?"
   ↓
Devices on the local LAN

For a 192.168.1.0/24 network, the IPv4 directed broadcast address is:

192.168.1.255

Routers normally do not forward Layer 2 broadcasts between networks.

Multicast

Multicast is one-to-many communication to a specific group of receivers.

One Sender → Selected Group

Example:

Server
 ├── Device A ✅ Group Member
 ├── Device B ❌
 ├── Device C ✅ Group Member
 └── Device D ❌

IPv4 multicast addresses are in the range:

224.0.0.0 – 239.255.255.255

Example:

224.0.0.1

is an IPv4 multicast address.

Communication Types
Unicast   → One → One
Broadcast → One → All
Multicast → One → Selected Group
IPv4 Practical Communication Flow

Consider a laptop with:

IP Address      → 192.168.1.74
Subnet Mask     → 255.255.255.0
Default Gateway → 192.168.1.1
DNS Server      → 192.168.1.1

The user enters:

https://google.com
Step 1 — DNS

The domain name must be resolved to an IP address.

google.com
    ↓
DNS
    ↓
Google IP
Step 2 — TCP

The browser establishes a TCP connection using the Three-Way Handshake.

SYN
SYN + ACK
ACK
Step 3 — Determine the Next Hop

Google is on a remote network, so the laptop sends the traffic toward its default gateway.

Laptop
   ↓
Default Gateway
   ↓
Internet
   ↓
Google
Step 4 — ARP

The laptop needs the router's MAC address for local Layer 2 delivery.

If the MAC address is not already known:

ARP Request → Broadcast
ARP Reply   → Unicast
Step 5 — Encapsulation

The data is encapsulated as it moves down the networking stack.

Application Data
       ↓
TCP Segment
       ↓
IP Packet
       ↓
Frame
       ↓
Bits

The IP packet contains:

Source IP      → Laptop IP
Destination IP → Google IP

The local frame contains:

Source MAC      → Laptop MAC
Destination MAC → Router MAC
Step 6 — Router Processing

The router receives the frame and processes the IP packet.

It checks:

Destination IP
      ↓
Routing Table
      ↓
Best Route
      ↓
Next Hop / Outgoing Interface

If the next-hop MAC address is unknown, the router can use ARP on that local network.

Step 7 — New Frame

The router creates a new Layer 2 frame for the next hop.

Old Frame
    ↓
Router
    ↓
Routing Decision
    ↓
New Frame
    ↓
Next Hop

MAC addresses change at each Layer 2 hop, while the destination IP normally remains the same during routing.

Step 8 — Response

When Google sends the response back:

Request:
Source IP      → Laptop IP
Destination IP → Google IP

The response reverses the IP direction:

Response:
Source IP      → Google IP
Destination IP → Laptop IP

The TCP ports also reverse:

Request:
Laptop:53124 → Google:443

Response:
Google:443 → Laptop:53124
Key Practical Rules
Same Network
    ↓
Direct local delivery
    ↓
Destination device MAC is required
Different Network
    ↓
Default Gateway
    ↓
Gateway MAC is required for the first local hop
Routing Table
    ↓
Determines where the IP packet should go next
ARP
    ↓
Finds the MAC address for a known IPv4 address
    ↓
Local network only
Important Layer Distinction
IP Packet → Layer 3
Frame     → Layer 2
Bits      → Physical Layer

A packet can be carried inside a frame:

IP Packet
    ↓
Frame
    ↓
Bits
IPv4 Special Address Summary
127.0.0.0/8      → Loopback
169.254.0.0/16   → APIPA / Link-local
10.0.0.0/8       → Private
172.16.0.0/12    → Private
192.168.0.0/16   → Private
224.0.0.0/4      → Multicast
IPv4 Foundation Completed

Topics covered:

IPv4 structure
Static IP
Dynamic IP
DHCP
DHCP DORA
DHCP UDP ports 67/68
DHCP lease and renewal
Loopback
APIPA
Unicast
Broadcast
Multicast
Default Gateway
ARP for local delivery
Same-network vs different-network communication
Routing Table
IP Packet vs Frame
Hop-by-hop Layer 2 delivery
New frame at each router
End-to-end IP addressing
Practical browser-to-server communication
