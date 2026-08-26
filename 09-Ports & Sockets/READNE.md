# Ports & Sockets

## 1. What is a Port?

A port is a 16-bit logical number used by the operating system to identify network services/applications.

Port range:

0 – 65535

IP address identifies the device, while the port helps identify the network service/application on that device.

Example:

192.168.1.10:443

192.168.1.10 = IP address
443 = Port

Memory:

IP Address → Which device?
Port → Which service/application?

---

## 2. Source Port vs Destination Port

Source Port:
The port used by the sender/client.

Destination Port:
The port of the receiving service/server.

Example:

192.168.1.10:54321 → Server:443

54321 = Source Port
443 = Destination Port

For the response, the direction is reversed:

Server:443 → 192.168.1.10:54321

---

## 3. Port Ranges

### Well-known Ports

0 – 1023

Examples:

HTTP  → 80
HTTPS → 443
SSH   → 22
DNS   → 53
FTP   → 21
SMTP  → 25

### Registered Ports

1024 – 49151

These ports are commonly associated with applications and services.

### Dynamic / Ephemeral Ports

49152 – 65535

These ports are commonly used temporarily by client-side connections.

Example:

192.168.1.10:55000 → Server:443

55000 = Ephemeral Port

---

## 4. Socket

A socket is an endpoint of network communication.

A socket is commonly identified using:

IP Address + Port Number + Transport Protocol

Example:

192.168.1.10:55000:TCP

IP Address → 192.168.1.10
Port       → 55000
Protocol   → TCP

---

## 5. Listening Port

A service can listen on a port for incoming connections.

Example:

LISTEN  0  128  0.0.0.0:22

This means the system is listening for TCP connections on port 22.

Port 22 is commonly associated with SSH.

---

## 6. LISTEN vs ESTABLISHED

LISTEN:

The service is waiting for incoming connections.

ESTABLISHED:

A TCP connection has been successfully established.

Example:

ESTABLISHED
192.168.1.20:54321 → 192.168.1.10:22

This indicates an active TCP connection.

---

## 7. TIME-WAIT

TIME-WAIT is a TCP state that occurs after a connection has been closed.

Example:

TIME-WAIT
192.168.1.66:43320 → 172.66.147.243:443

43320 = Ephemeral source port
443   = HTTPS destination port

TIME-WAIT does not mean the connection is currently active.

---

## 8. 127.0.0.1 vs 0.0.0.0

127.0.0.1:8080

→ Service is listening only on the local machine (loopback).

0.0.0.0:8080

→ Service is listening on all IPv4 interfaces.

---

## 9. Useful ss Commands

Show TCP listening sockets:

ss -tlnt

Show UDP listening sockets:

ss -ulnt

Show TCP/UDP listening sockets with process information:

ss -tulnp

Show TCP sockets:

ss -ant

---

## 10. SOC Perspective

A listening port does not automatically mean that a system is compromised.

If an unknown port is listening, investigate:

- Which process owns the port?
- Process ID (PID)
- Executable path
- Parent process
- User running the process
- Command line
- Network connections
- Destination IPs and ports

Example:

ss -tulnp

A SOC analyst should investigate an unknown listening service rather than judging it only by its port number.

---

## Key Memory

LISTEN       → Waiting for connections
ESTABLISHED  → Active TCP connection
TIME-WAIT    → Connection closed, TCP cleanup state

127.0.0.1 → Local machine only
0.0.0.0   → All IPv4 interfaces

IP + Port + Protocol → Network endpoint information
