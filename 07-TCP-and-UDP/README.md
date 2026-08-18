# TCP & UDP

## TCP

TCP stands for Transmission Control Protocol.

TCP is a Layer 4 (Transport Layer) protocol that provides reliable, connection-oriented, and ordered data transmission.

### Key Characteristics of TCP

- Connection-oriented
- Reliable
- Ordered delivery
- Uses acknowledgments
- Uses sequence numbers
- Retransmits lost segments
- Provides flow control
- Provides congestion control

---

## TCP Connection-Oriented

TCP establishes a logical connection between the sender and receiver before transmitting data.

### Three-Way Handshake

```text
Client                    Server

SYN -------------------->

     <------------------ SYN + ACK

ACK -------------------->

Connection Established
```
Handshake Steps
SYN → Client requests a TCP connection.
SYN + ACK → Server acknowledges the request and responds.
ACK → Client acknowledges the server.

After the three-way handshake, data transmission can begin.

TCP Flags
SYN — Synchronize

Used to establish a TCP connection and synchronize sequence numbers.

ACK — Acknowledgment

Used to acknowledge received TCP data/segments.

FIN — Finish

Used to gracefully terminate a TCP connection.

RST — Reset

Used to immediately reset or abort a TCP connection.

PSH — Push

Indicates that buffered data should be promptly delivered to the application.

URG — Urgent

Indicates that the segment contains urgent data.

TCP Connection Termination

TCP normally uses FIN to gracefully terminate a connection.

A typical termination sequence is:

Client                    Server


FIN --------------------->


     <------------------ ACK


     <------------------ FIN


ACK --------------------->
TCP Reliability

TCP provides reliable delivery using several mechanisms.

Sequence Number

Sequence numbers help TCP track bytes and maintain the correct order of data.

Acknowledgment Number

The ACK number indicates the next byte the receiver expects.

Example:

Sequence Number = 1000
Data = 100 bytes


Next expected byte = 1100
ACK = 1100
Retransmission

If a TCP segment is lost or the expected acknowledgment is not received, TCP can retransmit the data.

Duplicate Segments

If a segment is retransmitted even though the original already arrived, the receiver can use sequence numbers to identify duplicate data.

TCP Flow Control

Flow control prevents the sender from overwhelming the receiver.

TCP uses the Receive Window (rwnd) to indicate how much additional data the receiver can accept.

Fast Sender
     |
     |  Data
     v
Slow Receiver

Flow Control protects the receiver.

TCP Congestion Control

Congestion control helps prevent excessive traffic from overwhelming the network.

TCP adjusts its sending rate according to network conditions.

Difference
Flow Control
→ Protects the receiver


Congestion Control
→ Protects the network
UDP

UDP stands for User Datagram Protocol.

UDP is a Layer 4 (Transport Layer), connectionless protocol that provides low-overhead communication without guaranteeing reliable or ordered delivery.

Key Characteristics
Connectionless
Low overhead
No three-way handshake
No built-in acknowledgment
No built-in retransmission
No guaranteed ordering
Useful for latency-sensitive applications
UDP Communication

Unlike TCP, UDP does not establish a connection before sending data.

Client -------- UDP Data --------> Server
TCP vs UDP
Feature	TCP	UDP
Connection	Connection-oriented	Connectionless
Reliability	Reliable	No delivery guarantee
Ordering	Ordered	No ordering guarantee
Handshake	Three-way handshake	No handshake
Retransmission	Yes	No built-in retransmission
Flow Control	Yes	No TCP-style flow control
Congestion Control	Yes	No TCP-style congestion control
Overhead	Higher	Lower
Common Ports
Service	Protocol	Port
HTTP	TCP	80
HTTPS	TCP	443
SSH	TCP	22
DNS	UDP/TCP	53
DHCP Server	UDP	67
DHCP Client	UDP	68
Ephemeral Ports

Client applications commonly use temporary high-numbered source ports called ephemeral ports.

Example:

Client: 192.168.1.10:54321
Server: 8.8.8.8:53
Protocol: UDP

Here 54321 is the client-side source port.

Practical Wireshark Analysis
TCP Three-Way Handshake
SYN
SYN + ACK
ACK

Indicates TCP connection establishment.

TCP Reset
SYN
RST, ACK

Can indicate that a connection attempt was rejected or reset.

TCP Termination
FIN
ACK
FIN
ACK

Indicates graceful connection termination.

Possible Port Scanning

Repeated TCP SYN packets targeting many ports or hosts can be an indication of possible reconnaissance or port scanning.

However, SYN traffic alone does not prove malicious activity. Legitimate scanners and monitoring tools can also generate similar traffic.

Important Concepts
TCP
→ Reliable
→ Connection-oriented
→ Ordered
→ SYN / ACK / FIN / RST
→ Sequence Numbers
→ Retransmission
→ Flow Control
→ Congestion Control


UDP
→ Connectionless
→ Low overhead
→ No built-in reliability
→ No guaranteed ordering
→ Commonly used by DNS and DHCP
SOC Relevance

A SOC analyst should be able to identify:

TCP vs UDP traffic
Source and destination ports
TCP flags
Three-way handshake
Connection termination
Retransmissions
Suspicious SYN patterns
Possible port scanning
Listening and established connections
