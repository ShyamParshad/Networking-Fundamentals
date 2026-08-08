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

