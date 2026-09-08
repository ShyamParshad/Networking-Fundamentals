# Wireshark

## 1. What is Wireshark?

Wireshark is a network protocol analyzer used to capture and inspect network traffic.

It allows us to examine individual packets and understand what happened during network communication.

Basic flow:

```text
Network Traffic
      ↓
Wireshark captures packets
      ↓
Packet Analysis
      ↓
Understand network communication
```

Wireshark can be used to analyze protocols such as:

- ARP
- ICMP
- IPv4
- TCP
- UDP
- DNS
- TLS


## 2. Wireshark Packet View

Wireshark commonly provides three important areas:

```text
Packet List
     ↓
Packet Details
     ↓
Packet Bytes
```

### Packet List

Shows captured packets and basic information such as:

- Packet number
- Time
- Source
- Destination
- Protocol
- Length
- Info

### Packet Details

Shows protocol fields for the selected packet.

Example:

```text
Ethernet II
Internet Protocol Version 4
Transmission Control Protocol
```

### Packet Bytes

Shows the raw packet data in hexadecimal and ASCII representation.


## 3. Packet Layer Structure

A captured packet can contain multiple protocol layers.

Example:

```text
Ethernet
   ↓
IPv4
   ↓
TCP
   ↓
Application Data
```

For an ICMP packet:

```text
Ethernet
   ↓
IPv4
   ↓
ICMP
   ↓
Data
```

For DNS traffic:

```text
Ethernet
   ↓
IPv4
   ↓
UDP
   ↓
DNS
```


## 4. ICMP Packet Analysis

A ping generates ICMP Echo Request and Echo Reply packets.

Example:

```text
192.168.1.71 → 8.8.8.8
ICMP Echo Request

8.8.8.8 → 192.168.1.71
ICMP Echo Reply
```

Common ICMP fields:

```text
Type
Code
Checksum
Identifier
Sequence Number
```

For an Echo Request:

```text
Type = 8
Code = 0
```

For an Echo Reply:

```text
Type = 0
Code = 0
```

### Practical Capture

A ping was generated using:

```bash
ping -c 4 8.8.8.8
```

Wireshark showed the request and reply packets.

Example packet:

```text
Source IP      = 192.168.1.71
Destination IP = 8.8.8.8
Protocol       = ICMP
TTL            = 64
```

The corresponding reply had:

```text
Source IP      = 8.8.8.8
Destination IP = 192.168.1.71
TTL            = 118
```

This demonstrated how ICMP theory appears in an actual packet capture.


## 5. IPv4 Header Analysis

The IPv4 header of the captured ICMP packet contained fields such as:

```text
Version
Header Length
Differentiated Services Field
Total Length
Identification
Flags
Fragment Offset
Time to Live
Protocol
Header Checksum
Source Address
Destination Address
```

Example captured values:

```text
Version           = 4
Header Length     = 20 bytes
Total Length      = 84 bytes
Flags             = Don't Fragment
Fragment Offset   = 0
TTL               = 64
Protocol          = ICMP (1)
Source Address    = 192.168.1.71
Destination       = 8.8.8.8
```

Important observation:

```text
Protocol = ICMP (1)
```

This indicates that the IPv4 packet carries ICMP as its upper-layer protocol.


## 6. Ethernet and MAC Address Analysis

The captured ICMP packet also contained an Ethernet header.

Example:

```text
Source MAC
00:0c:29:7a:63:98

Destination MAC
54:37:bb:93:51:04
```

The destination IP was:

```text
8.8.8.8
```

but the local Ethernet destination MAC was the MAC address of the next hop/gateway.

This demonstrates the relationship between routing and ARP:

```text
Destination IP
      ↓
Routing decision
      ↓
Next Hop / Gateway
      ↓
ARP / Neighbor Resolution
      ↓
Next Hop MAC
      ↓
Ethernet Frame
```

Important rule:

> Routing decides the next hop. ARP finds the MAC address of the next hop.


## 7. TCP 3-Way Handshake Analysis

A TCP connection is established using a 3-way handshake:

```text
Client                         Server

SYN
──────────────────────────────→

             SYN + ACK
←──────────────────────────────

ACK
──────────────────────────────→
```

### Captured TCP Handshake

Client:

```text
192.168.1.71:41914
```

Server:

```text
172.66.147.243:443
```

### Packet 1 — SYN

```text
192.168.1.71:41914
        ↓
172.66.147.243:443
```

Important fields:

```text
Source Port      = 41914
Destination Port = 443
Flags            = SYN
Sequence Number  = 0 (relative)
Acknowledgment   = 0
```

Meaning:

> The client is requesting to establish a TCP connection to the server's port 443.


### Packet 2 — SYN-ACK

```text
172.66.147.243:443
        ↓
192.168.1.71:41914
```

Important fields:

```text
Flags                  = SYN, ACK
Sequence Number        = 0 (relative)
Acknowledgment Number  = 1
```

The ACK value is `1` because the client's SYN consumes one sequence number.


### Packet 3 — Final ACK

```text
192.168.1.71:41914
        ↓
172.66.147.243:443
```

Important fields:

```text
Flags                 = ACK
Sequence Number       = 1
Acknowledgment Number = 1
TCP Segment Length    = 0
```

This completes the TCP 3-way handshake.


## 8. TCP Sequence and Acknowledgment Numbers

TCP sequence numbers track the position of data in the TCP byte stream.

TCP acknowledgment numbers indicate the next sequence number expected from the other side.

Example from the capture:

```text
Client sends:

Sequence Number = 1
TCP Segment Length = 1698
```

Therefore:

```text
1 + 1698 = 1699
```

The server acknowledges:

```text
Acknowledgment Number = 1699
```

Meaning:

> The server received the data and expects sequence number 1699 next.

Important rule:

> Sequence Number = position of data in the TCP stream.

> Acknowledgment Number = next sequence number expected.


## 9. TCP Data Exchange

After the 3-way handshake, application data can be exchanged.

In the captured HTTPS connection:

```text
TCP Handshake
      ↓
SYN
      ↓
SYN-ACK
      ↓
ACK
      ↓
TLS 1.3 Client Hello
      ↓
Further TLS/Application Data
```

The captured Client Hello packet contained:

```text
Source       = 192.168.1.71:41914
Destination  = 172.66.147.243:443
Sequence     = 1
Acknowledgment = 1
TCP Segment Length = 1698
Flags        = PSH, ACK
```

This showed how TCP carries application-layer data after the connection is established.


## 10. TCP Flags Observed in Wireshark

Important TCP flags observed during analysis:

```text
SYN
ACK
PSH
```

### SYN

Used during TCP connection establishment.

### ACK

Acknowledges received data or sequence information.

### PSH

Indicates that the receiver should make the available data available to the application promptly.


## 11. DNS Packet Analysis

DNS traffic was captured using the filter:

```text
dns
```

A DNS query was generated using:

```bash
nslookup example.com
```

### DNS Query

Captured packet:

```text
Source      = 192.168.1.71
Destination = 192.168.1.254
Protocol    = DNS
```

UDP details:

```text
Source Port      = 40001
Destination Port = 53
```

The query contained:

```text
Transaction ID = 0x6052
Query Type     = A
Domain         = example.com
```

Meaning:

> The client asked the DNS server for the IPv4 address of `example.com`.


## 12. DNS Response

The DNS response was:

```text
192.168.1.254 → 192.168.1.71
```

The response used the same transaction ID:

```text
0x6052
```

The response returned IPv4 addresses for `example.com`, including:

```text
172.66.147.243
104.20.23.154
```

The same capture also showed an AAAA query:

```text
Query Type = AAAA
Domain     = example.com
```

AAAA records are used for IPv6 addresses.


## 13. Wireshark Filters Used

### ICMP

```text
icmp
```

### TCP

```text
tcp
```

### DNS

```text
dns
```

### TCP Retransmission

```text
tcp.analysis.retransmission
```

### TCP Duplicate ACK

```text
tcp.analysis.duplicate_ack
```

No retransmission or duplicate ACK packets were detected in the tested capture.


## 14. Packet Analysis Method

A useful method for analyzing a packet is:

```text
1. Source
      ↓
2. Destination
      ↓
3. Protocol
      ↓
4. Transport information
      ↓
5. Important flags/fields
      ↓
6. Application protocol
      ↓
7. Interpret what happened
```

Example:

```text
192.168.1.71:41914
        ↓
172.66.147.243:443
        ↓
TCP
        ↓
SYN
        ↓
Connection establishment
```

Another example:

```text
192.168.1.71
        ↓
192.168.1.254
        ↓
UDP
        ↓
DNS
        ↓
A example.com
        ↓
IPv4 address response
```


## 15. SOC-Relevant Takeaways

Wireshark can help an analyst investigate:

- Source and destination IP addresses
- Source and destination ports
- Protocols
- TCP connection establishment
- TCP acknowledgments
- DNS queries and responses
- ICMP traffic
- Packet-level communication patterns
- Suspicious or unexpected network activity

Important principle:

> Do not interpret a packet only by its port or protocol. Always examine the complete communication context.


## 16. Key Takeaways

- Wireshark is a network protocol analyzer.
- Packet captures show communication at multiple protocol layers.
- Ethernet provides Layer 2 information such as MAC addresses.
- IPv4 provides Layer 3 information such as source and destination IPs.
- ICMP Echo Request and Echo Reply are used by ping.
- TCP uses the SYN → SYN-ACK → ACK 3-way handshake.
- TCP sequence and acknowledgment numbers track the byte stream.
- TCP data can be carried after the handshake.
- DNS commonly uses UDP port 53 for queries.
- A records provide IPv4 addresses.
- AAAA records provide IPv6 addresses.
- Wireshark filters make it easier to isolate specific protocols.
- Packet analysis helps understand what actually happened on the network.
