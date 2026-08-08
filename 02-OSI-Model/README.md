# OSI Model

## Why was the OSI Model created?

Before the OSI Model, different companies used different networking rules and standards. Because of this, devices from one company could not communicate properly with devices from another company.

To solve this problem, a common networking standard was introduced. This standard is known as the OSI (Open Systems Interconnection) Model.


## What is the OSI Model?

The OSI (Open Systems Interconnection) Model is a conceptual model that explains how data travels from one device to another over a network.

It divides network communication into seven layers, and each layer has a specific responsibility.

## Application Layer (Layer 7)

The Application Layer is the top layer of the OSI Model. It provides network services to applications such as web browsers, email clients, and messaging applications.

**Examples:**
- Google Chrome
- Microsoft Outlook
- WhatsApp
- Firefox

---

## Presentation Layer (Layer 6)

The Presentation Layer is responsible for presenting data in a suitable format. It performs encryption, decryption, compression, and decompression before the data is sent or after it is received.

**Example:**
- HTTPS uses encryption to protect data while communicating over the Internet.

---

## Session Layer (Layer 5)

The Session Layer is responsible for establishing, maintaining, and terminating communication sessions between two devices.

**Example:**
- A WhatsApp or Zoom video call starts a session, keeps it active during the conversation, and closes it when the call ends.

---

## Transport Layer (Layer 4)

The Transport Layer is responsible for end-to-end communication between devices. It uses TCP for reliable communication and UDP for faster, connectionless communication. It also divides data into segments and uses port numbers to deliver data to the correct application.

### TCP
- Connection-oriented
- Reliable communication
- Uses Three-Way Handshake
- Retransmits lost packets

### UDP
- Connectionless
- Faster communication
- Does not retransmit lost packets
- Used for real-time applications

---

## Network Layer (Layer 3)

The Network Layer is responsible for logical addressing, routing, and packet forwarding. It uses IP addresses to identify the source and destination devices. Routers work at this layer and find the best path for packets to reach the destination.

### Main Responsibilities

- Logical Addressing (IP Address)
- Routing
- Packet Forwarding
- Best Path Selection

### Example

When I open google.com, the Network Layer uses the destination IP address and sends the packet through different routers until it reaches Google's server.

---

## Data Link Layer (Layer 2)

The Data Link Layer is responsible for communication within a Local Area Network (LAN). It uses MAC addresses to deliver frames between devices connected to the same network. Switches work at this layer.

### Main Responsibilities

- MAC Addressing
- Frame Delivery
- Error Detection
- Communication inside LAN

### ARP

If a device knows the destination IP address but not the MAC address, it sends an ARP Request (Broadcast). The destination device replies with an ARP Reply (Unicast) containing its MAC address.

### Example

Before sending data to another device in the same LAN, my laptop uses ARP to find the destination MAC address.

---

## Physical Layer (Layer 1)

The Physical Layer is the lowest layer of the OSI Model. It is responsible for transmitting data as bits (0s and 1s) through physical media such as Ethernet cables, fiber optics, or wireless signals.

### Main Responsibilities

- Transmit Bits (0 and 1)
- Electrical Signals
- Optical Signals
- Wireless Signals

### Example

When I connect my laptop to a router using an Ethernet cable, the Physical Layer sends bits through the cable as electrical signals.

**Examples:**
- TCP: File download, Email, Banking
- UDP: Online gaming, Video calls, Live streaming
