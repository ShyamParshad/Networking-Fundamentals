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
