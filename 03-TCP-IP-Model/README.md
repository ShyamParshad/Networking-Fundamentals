# TCP/IP Model

## What is the TCP/IP Model?

The TCP/IP Model is a practical networking model used for communication over the Internet. It is simpler than the OSI Model and combines some OSI layers to make real-world implementation easier.

The TCP/IP Model has four layers:

1. Application Layer
2. Transport Layer
3. Internet Layer
4. Network Access Layer

## Why is the TCP/IP Model used in the real world?

The TCP/IP Model is used in real-world networking because it is simpler and more practical than the OSI Model. It combines some OSI layers, which reduces complexity and makes implementation easier. Today, the Internet uses the TCP/IP Model for communication.

## Application Layer

The Application Layer is the top layer of the TCP/IP Model. It provides network services to applications such as web browsers, email clients, and messaging applications.

Unlike the OSI Model, the TCP/IP Application Layer also includes the responsibilities of the Presentation Layer (encryption, decryption, formatting, compression) and the Session Layer (session establishment, maintenance, and termination).

### Common Protocols
- DNS – Converts domain names into IP addresses.
- HTTP – Transfers web pages.
- HTTPS – Secure version of HTTP using encryption.
- SMTP – Sends emails.
- FTP – Transfers files between systems.

### Example

When I type www.google.com in my browser:

1. DNS finds Google's IP address.
2. The browser uses HTTPS to send a request.
3. Google returns the webpage.

---

# Transport Layer

The Transport Layer is responsible for end-to-end communication between devices. It ensures that data is delivered to the correct application using port numbers. This layer mainly uses two protocols: TCP and UDP.

## TCP (Transmission Control Protocol)

TCP is a connection-oriented and reliable protocol. Before sending data, it establishes a connection using the Three-Way Handshake. If any segment is lost, TCP retransmits it to ensure complete and accurate delivery.

### Common Uses

- Web Browsing (HTTP/HTTPS)
- File Downloads
- Email
- Online Banking

### Example

When downloading a PDF file, TCP ensures that every segment reaches the destination correctly. If a segment is lost, TCP retransmits it.

---

## UDP (User Datagram Protocol)

UDP is a connectionless protocol. It does not establish a connection before sending data and does not retransmit lost packets. It is faster than TCP and is used where speed is more important than perfect reliability.

### Common Uses

- Online Gaming
- Video Streaming
- Voice Calls (VoIP)
- Live Streaming

### Example

Online games use UDP because receiving the latest game data quickly is more important than retransmitting lost packets.

---

## TCP Three-Way Handshake

Before sending data, TCP establishes a connection using three steps:

1. **SYN** – The client requests a connection.
2. **SYN + ACK** – The server acknowledges the request and agrees to connect.
3. **ACK** – The client confirms the server's response.

After these three steps, the TCP connection is established and data transfer begins.

### Port Numbers

Port numbers help identify the correct service or application on a device.

Common ports:

- HTTP → 80
- HTTPS → 443
- DNS → 53
- SSH → 22
- RDP → 3389

Example:

Source: 192.168.1.74:53124
Destination: 142.250.x.x:443

Here, 53124 is a temporary source port and 443 is the destination port for HTTPS.

### Source Port and Destination Port

The source port is usually a temporary port assigned by the client device.

The destination port identifies the service the client wants to communicate with.

For example:

text
192.168.1.74:53124 → 142.250.x.x:443

TCP Segmentation

TCP divides large amounts of application data into smaller segments before transmission.

Large Data
    ↓
TCP
    ↓
Segment 1
Segment 2
Segment 3

Sequence Numbers

TCP uses sequence numbers to keep track of transmitted data and maintain the correct order of segments.

If a segment is lost, sequence numbers help TCP identify the missing data.

Acknowledgment (ACK)

TCP uses acknowledgments to confirm that data has been successfully received.

Retransmission

If TCP detects that data has been lost, it can retransmit the missing data to provide reliable delivery.

Flow Control

TCP uses flow control to prevent a fast sender from overwhelming a slower receiver.

The receiver advertises a receive window to tell the sender how much data it can currently accept.

Without flow control, a fast sender could send data faster than the receiver can process it, which could fill the receiver's buffer.

TCP Reliability

TCP provides reliable communication using multiple mechanisms:

Sequence Numbers
Acknowledgments
Retransmission
Flow Control

---

## Internet Layer

The Internet Layer is responsible for logical addressing and routing packets between networks. It mainly uses IP addresses to identify the source and destination of network communication.

### IP Address

An IP address is a logical address used to identify and locate a device or network interface on an IP network. It helps routers determine where packets should be forwarded.

### Source IP and Destination IP

When a device sends a packet, the packet contains a source IP address and a destination IP address.

Example:

```text
Source IP:      192.168.1.74
Destination IP: 142.250.x.x
```

- **Source IP** → identifies where the packet came from.
- **Destination IP** → identifies where the packet is going.

### Routing

Routers use routing tables to determine where packets should be forwarded.

A routing table can contain information such as:

- Destination Network
- Next Hop
- Outgoing Interface

The router checks the destination IP against its routing table and selects the best available route.

```text
Destination Network   Next Hop       Interface
142.250.0.0/16        10.0.0.1       eth1
```

### Next Hop

A next hop is the next router or device to which a packet is forwarded on its journey toward the destination.

```text
Laptop → Router 1 → Router 2 → Router 3 → Server
```

Each router makes its own forwarding decision based on its routing table.

### IP Packet

When data reaches the Internet Layer, an IP header is added to the transport-layer data, creating an IP packet.

```text
Application Data
       ↓
TCP Segment
       ↓
IP Packet
       ↓
Frame
       ↓
Bits
```

An IP packet contains important information such as:

- Source IP
- Destination IP
- Other IP header information
- Transport-layer data

### IP Address vs MAC Address

IP addresses are used for logical addressing and routing, while MAC addresses are used for communication on the local network.

text
IP Address  → Logical addressing / routing
MAC Address → Local network communication


## Network Access Layer

The Network Access Layer is responsible for delivering data across the local network. In the TCP/IP model, it combines the functions of the OSI Data Link Layer and Physical Layer.

### MAC Address

A MAC (Media Access Control) address is a Layer 2 address associated with a network interface. It is used for communication between devices on the local network.

text
IP Address  → Logical addressing and routing
MAC Address → Local hop-to-hop communication

ARP

ARP (Address Resolution Protocol) is used to find the MAC address associated with a known IPv4 address on the local network.

For example, if a laptop knows the router's IP address but does not know its MAC address, it sends an ARP Request.

ARP Request → Broadcast

"Who has 192.168.1.1?"

The router responds with an ARP Reply:
ARP Reply → Unicast

"192.168.1.1 is my IP.
My MAC is AA:BB:CC:DD:EE:FF."

ARP Request → Broadcast
ARP Reply → Unicast

ARP works within the local network and is not forwarded across routers.

Frame

At the Data Link layer, an IP packet is encapsulated inside a frame.

A basic Ethernet frame contains information such as:

Destination MAC
Source MAC
EtherType
IP Packet
FCS

TCP Segment
     ↓
IP Packet
     ↓
Frame

Hop-by-Hop MAC Addressing

When a packet travels through multiple routers, the Layer 2 frame changes at every hop.

Example:

Laptop → Router 1 → Router 2 → Google

First hop:

Source MAC      = Laptop MAC
Destination MAC = Router 1 MAC

Second hop:

Source MAC      = Router 1 MAC
Destination MAC = Router 2 MAC

The destination IP normally remains the same while the Layer 2 MAC addresses change at each hop.

New Frame at Each Router

A router does not simply forward the old Layer 2 frame. It removes the old frame and creates a new frame for the next hop.

Old Frame
    ↓
Router
    ↓
IP Packet is processed
    ↓
Routing Decision
    ↓
New Frame
    ↓
Next Hop

## Physical Layer

The Physical Layer is responsible for transmitting raw data as bits (0s and 1s) through a physical or wireless medium.

It deals with the actual transmission of signals that carry the bits from one device to another.

### Main Responsibilities

- Transmits data as bits (0s and 1s)
- Converts bits into physical signals
- Carries signals through the transmission medium
- Defines aspects of the physical connection between devices

### Transmission Media

Data can be transmitted through different physical or wireless media, such as:

- Ethernet cables
- Fiber-optic cables
- Radio signals used by Wi-Fi

### Example

When a device sends data:

text
Frame
  ↓
Physical Layer
  ↓
Bits (0s and 1s)
  ↓
Electrical / Optical / Radio Signals
  ↓
Transmission Medium

At the receiving device, the physical signals are converted back into bits.

Transmission Medium
        ↓
Physical Signals
        ↓
Bits (0s and 1s)
        ↓
Frame

The Physical Layer works with:

Bits (0s and 1s)

Key Point

The Physical Layer is responsible for the actual transmission of bits over a physical or wireless medium.

It does not understand IP addresses, MAC addresses, ports, or applications. Its primary responsibility is the physical transmission of bits.
