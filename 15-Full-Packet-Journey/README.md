# Full Packet Journey

## 1. What is a Full Packet Journey?

A Full Packet Journey explains what happens when a device communicates with a remote server.

It connects the networking concepts learned throughout the Networking Fundamentals phase into one complete flow.

Example:

```text
Kali
192.168.1.71
      ↓
https://example.com
      ↓
DNS
      ↓
Destination IP
      ↓
Routing
      ↓
ARP
      ↓
Ethernet
      ↓
Router / Gateway
      ↓
NAT/PAT
      ↓
Internet
      ↓
TCP
      ↓
TLS
      ↓
HTTPS Application Data
      ↓
Server
```

## 2. Step 1 — DNS Resolution

When a user enters:

```text
https://example.com
```

the system first needs the server's IP address.

The client sends a DNS query.

```text
example.com
     ↓
DNS Query
     ↓
DNS Server
     ↓
IP Address
```

Example:

```text
example.com → 172.66.147.243
```

Now the client knows the destination IP.

**Important:** DNS resolves a domain name to an IP address.

## 3. Step 2 — Routing Decision

The client checks its routing table to determine how to reach the destination.

Example:

```text
Kali IP     = 192.168.1.71
Local Network = 192.168.1.0/24
Destination = 172.66.147.243
```

The destination is not inside the local network.

The routing table contains:

```text
default via 192.168.1.254 dev eth0
```

Therefore:

```text
Destination
172.66.147.243
       ↓
Not directly connected
       ↓
Default Route
       ↓
Next Hop
192.168.1.254
```

**Important:** Routing determines the next hop toward the destination.

## 4. Step 3 — ARP

The system knows the next hop IP:

```text
192.168.1.254
```

But Ethernet needs a MAC address for the local frame.

ARP is used to find the MAC address associated with the next-hop IP.

Example:

```text
192.168.1.254
      ↓
ARP
      ↓
54:37:bb:93:51:04
```

The client can now construct the Ethernet frame.

**Important:** ARP finds the MAC address of the local next hop.

## 5. Step 4 — Ethernet Frame

The packet is placed inside an Ethernet frame for transmission on the local network.

Example:

```text
Source MAC
Kali MAC

Destination MAC
Gateway MAC
54:37:bb:93:51:04
```

At the same time the IP packet contains:

```text
Source IP
192.168.1.71

Destination IP
172.66.147.243
```

**Important observation:**

The destination IP can be the remote server while the local Ethernet destination MAC is the gateway's MAC.

This happens because routing has selected the gateway as the next hop.

## 6. Step 5 — Router Forwarding

The gateway receives the Ethernet frame.

The router examines the destination IP:

```text
172.66.147.243
```

The router then uses its own routing table to determine the next path.

The packet can pass through multiple routers:

```text
Kali
  ↓
Router 1
  ↓
Router 2
  ↓
ISP / Internet
  ↓
Server Network
  ↓
172.66.147.243
```

At each routed hop:

- The Layer 2 frame is replaced for the next link.
- The IP destination normally remains the remote destination.
- The packet is forwarded according to routing decisions.

**Important:** MAC addresses are used hop-by-hop while IP addressing provides end-to-end Layer 3 addressing.

## 7. Step 6 — NAT/PAT

The local Kali uses a private IP:

```text
192.168.1.71
```

A NAT/PAT device can translate the private source into a public source address and port.

Conceptually:

```text
192.168.1.71:41914
        ↓
      PAT
        ↓
Public-IP:Translated-Port
        ↓
172.66.147.243:443
```

PAT allows multiple internal hosts to share a public IP by using different translated ports.

**Important:** NAT/PAT translates addresses and, with PAT, ports between network domains.

## 8. Step 7 — TCP Connection Establishment

The destination service is HTTPS on TCP port 443.

TCP establishes the connection using the 3-way handshake.

```text
Client                         Server

SYN
──────────────────────────────→

             SYN + ACK
←──────────────────────────────

ACK
──────────────────────────────→
```

Example:

```text
Client:
192.168.1.71:41914

Server:
172.66.147.243:443
```

The handshake establishes the TCP connection before normal application data exchange.

**Important:** TCP uses SYN → SYN-ACK → ACK to establish a connection.

## 9. Step 8 — TLS

After the TCP connection is established, HTTPS communication begins with TLS.

Example:

```text
TCP Handshake
      ↓
TLS 1.3
      ↓
Client Hello
      ↓
Server Hello
      ↓
TLS Handshake
      ↓
Encrypted Application Data
```

A Wireshark capture showed:

```text
TLSv1.3 Client Hello
```

after the TCP handshake.

**Important:** TCP provides the transport connection while TLS provides encryption and authentication mechanisms for HTTPS communication.

## 10. Step 9 — Application Data

After TCP and TLS setup, the client and server can exchange application data.

For HTTPS:

```text
Browser
   ↓
Encrypted HTTPS Data
   ↓
TLS
   ↓
TCP
   ↓
IP
   ↓
Ethernet
   ↓
Network
```

The server processes the request and generates a response.

## 11. Step 10 — Response

The response travels back toward the client.

Conceptually:

```text
Server
  ↓
Internet
  ↓
NAT/PAT Reverse Translation
  ↓
Gateway
  ↓
Kali
```

NAT/PAT uses its translation state to map returning traffic back to the correct internal host and port.

Then the data moves through the protocol stack back toward the browser.

## 12. Complete End-to-End Flow

```text
Browser
   ↓
example.com
   ↓
DNS Resolution
   ↓
Destination IP
   ↓
Routing Table
   ↓
Default Gateway / Next Hop
   ↓
ARP
   ↓
Next-Hop MAC
   ↓
Ethernet Frame
   ↓
Router Forwarding
   ↓
NAT/PAT
   ↓
Internet
   ↓
TCP 3-Way Handshake
   ↓
TLS
   ↓
HTTPS Application Data
   ↓
Server
   ↓
Response
   ↓
Reverse Path
   ↓
Browser
```

## 13. How Earlier Networking Topics Connect

### DNS

Finds the IP address of the destination.

```text
example.com
↓
172.66.147.243
```

### Routing

Determines the next hop.

```text
Destination
↓
Default Gateway
```

### ARP

Finds the MAC address of the local next hop.

```text
Gateway IP
↓
Gateway MAC
```

### Ethernet

Carries the frame across the local network link.

### IP

Provides Layer 3 addressing and forwarding between networks.

### NAT/PAT

Translates private addresses and ports when required.

### TCP

Provides reliable transport and establishes the connection.

### TLS

Provides encryption and security for HTTPS communication.

## 14. Wireshark View of the Journey

Wireshark can show different parts of this communication.

Example:

```text
DNS Query
      ↓
DNS Response
      ↓
TCP SYN
      ↓
TCP SYN-ACK
      ↓
TCP ACK
      ↓
TLS Client Hello
      ↓
TLS/Application Traffic
```

This allows an analyst to observe the communication at packet level.

## 15. Important Networking Rules

### Rule 1

> DNS finds the destination IP.

### Rule 2

> Routing decides the next hop.

### Rule 3

> ARP finds the next-hop MAC address on the local network.

### Rule 4

> Ethernet delivers the frame across the current local link.

### Rule 5

> Routers forward packets toward the destination.

### Rule 6

> NAT/PAT can translate private addresses and ports.

### Rule 7

> TCP establishes reliable transport.

### Rule 8

> TLS protects HTTPS communication.

## 16. SOC Analyst Perspective

A SOC analyst can use this packet journey to understand where a network problem or suspicious activity may occur.

For example:

```text
DNS problem
→ Domain does not resolve

Routing problem
→ No valid path to destination

ARP problem
→ Next-hop MAC cannot be resolved

TCP problem
→ Connection cannot be established

TLS problem
→ Secure session negotiation fails

Application problem
→ Connection exists but the application fails
```

This layered approach helps an analyst investigate network communication systematically.

## 17. Final Mental Model

Remember the complete journey as:

```text
NAME
 ↓
DNS

DESTINATION
 ↓
IP

NEXT HOP
 ↓
Routing

MAC
 ↓
ARP

FRAME
 ↓
Ethernet

NETWORK PATH
 ↓
Routers

ADDRESS TRANSLATION
 ↓
NAT/PAT

CONNECTION
 ↓
TCP

ENCRYPTION
 ↓
TLS

APPLICATION
 ↓
HTTPS
```

## Key Takeaway

> A network request is not just a single packet sent directly from one computer to another. Multiple protocols and layers work together to resolve the destination, select the path, deliver frames hop-by-hop, establish transport, secure the communication, and exchange application data.
