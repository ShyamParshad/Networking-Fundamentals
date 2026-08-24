# Firewall

## 1. What is a Firewall?

A firewall is a security control that monitors and controls network traffic based on predefined rules.

A firewall can decide whether traffic should be:

- ALLOWED
- BLOCKED / DROPPED

Basic flow:

Internet
   |
   v
Firewall
   |
   v
Internal Network

---

## 2. What Information Can a Firewall Inspect?

Depending on the firewall and inspection mode, common traffic attributes include:

- Source IP
- Destination IP
- Source Port
- Destination Port
- Protocol
- Connection State
- Direction
- Action

Example:

192.168.1.66:54321 -> 8.8.8.8:443
TCP

Source IP       = 192.168.1.66
Source Port     = 54321
Destination IP  = 8.8.8.8
Destination Port= 443
Protocol        = TCP

---

## 3. Firewall vs Antivirus / EDR

Firewall:

- Controls network traffic.
- Allows or blocks connections based on rules.

Antivirus / EDR:

- Monitors files, processes and endpoint behavior.
- Can detect and respond to malicious activity.

They provide different layers of security.

---

# 4. Types of Firewalls

## Packet Filtering Firewall

Makes decisions using packet-level information such as:

- Source IP
- Destination IP
- Protocol
- Port

Example:

TCP destination port 443 -> ALLOW

---

## Stateful Firewall

A stateful firewall tracks connection state and uses connection context when making decisions.

Example TCP connection:

Client -> SYN -> Server
Client <- SYN-ACK <- Server
Client -> ACK -> Server

Connection becomes:

ESTABLISHED

The firewall can recognize return traffic as part of an existing connection.

Memory:

Stateless = Packet by packet

Stateful = Packet + Connection State

---

## Next-Generation Firewall (NGFW)

NGFWs can provide advanced traffic inspection and security capabilities such as:

- Application awareness
- Deeper traffic inspection
- Threat prevention
- User or identity-based policies

Features depend on the firewall product.

---

# 5. Connection State

Connection state describes the current stage of a network connection.

Common TCP states include:

LISTEN
SYN-SENT
SYN-RECEIVED
ESTABLISHED
FIN-WAIT
CLOSE-WAIT
TIME-WAIT

### LISTEN

A service is waiting for incoming connections.

### SYN-SENT

A client has sent a TCP SYN and is waiting for a response.

### SYN-RECEIVED

The server has received a SYN and sent a SYN-ACK.

### ESTABLISHED

The TCP connection has been successfully established.

### TIME-WAIT

The TCP connection has been closed and is in a cleanup/wait state.

Example:

ESTABLISHED
192.168.1.66:41838 -> 151.101.1.91:443

This means the TCP connection is established.

---

# 6. Firewall Rules

Firewall rules define how matching traffic should be handled.

Example:

Protocol: TCP
Destination Port: 443
Action: ALLOW

This allows matching TCP traffic to destination port 443.

Another example:

Protocol: TCP
Destination Port: 23
Action: BLOCK

This blocks matching Telnet traffic.

---

# 7. ALLOW vs BLOCK / DROP

### ALLOW

Allows matching traffic.

Example:

TCP/443 -> ALLOW

### BLOCK / DROP

Prevents matching traffic from passing.

Example:

TCP/23 -> DROP

DROP usually means the packet is silently discarded.

---

# 8. Inbound vs Outbound Traffic

## Inbound

Traffic coming into a system or network.

Example:

Internet -> Server:443

This is inbound traffic.

## Outbound

Traffic leaving a system or network.

Example:

Kali -> Internet:443

This is outbound traffic.

Memory:

Inbound  = Outside -> Inside

Outbound = Inside -> Outside

---

# 9. Firewall Rule Order

Firewall rules are commonly evaluated in a defined order.

Example:

Rule 1: ALLOW TCP 443
Rule 2: BLOCK TCP 443

If the firewall uses first-match processing, traffic matching Rule 1 may be allowed before Rule 2 is evaluated.

Therefore:

Rule order matters.

Firewall configurations should be designed carefully to avoid unintended access.

---

# 10. Default Firewall Policy

A default policy determines what happens when traffic does not match a specific rule.

## Default Allow

Traffic is allowed unless explicitly blocked.

Example:

TCP/23 -> BLOCK
Everything else -> ALLOW

## Default Deny

Traffic is blocked unless explicitly allowed.

Example:

TCP/443 -> ALLOW
TCP/22 -> ALLOW
Everything else -> BLOCK

Default-deny follows a more restrictive allowlist-style approach.

---

# 11. Listening Port vs Firewall

A listening port and a firewall rule are different things.

A service can be listening on a port while the firewall blocks traffic to that port.

Example:

Python service
      |
      v
0.0.0.0:8080
      |
    LISTEN
      |
   Firewall
      |
      v
TCP/8080 -> DROP

The service is still running, but the firewall prevents matching traffic from reaching it.

Important:

A listening port does NOT automatically mean the port is accessible from the network.

---

# 12. 127.0.0.1 vs 0.0.0.0

## 127.0.0.1

127.0.0.1 is the loopback address.

Example:

127.0.0.1:8080

A service bound to this address is intended to accept connections through the local loopback interface only.

## 0.0.0.0

0.0.0.0 in a listening/bind context means all IPv4 interfaces.

Example:

0.0.0.0:8080

A service bound this way can listen on the system's IPv4 interfaces, subject to routing and firewall rules.

Important:

0.0.0.0 is not a normal destination address for connecting to the service.

---

# 13. Linux Firewall Tools

## UFW

UFW is a firewall management tool commonly used on Linux systems.

Check status:

sudo ufw status verbose

If UFW is not installed, the command may return:

ufw: command not found

---

## nftables

nftables is a Linux packet filtering framework.

View the current ruleset:

sudo nft list ruleset

If there are no configured rules, the command may produce no output.

---

# 14. Useful Linux Commands

Check listening TCP/UDP sockets:

ss -tulnp

Check TCP sockets:

ss -ant

Check IP addresses:

ip addr

Check routing table:

ip route

Check neighbor/ARP information:

ip neigh

Check nftables rules:

sudo nft list ruleset

---

# 15. Firewall Practical Lab

A Python HTTP server was started locally:

python3 -m http.server 8080 --bind 127.0.0.1

The listening socket was:

127.0.0.1:8080

This showed that the service was listening only on the loopback interface.

Then the server was bound to all IPv4 interfaces:

python3 -m http.server 8080 --bind 0.0.0.0

The socket became:

0.0.0.0:8080

It was verified using:

ss -tulnp | grep 8080

Example:

tcp LISTEN 0 5 0.0.0.0:8080 0.0.0.0:* users:(("python3",pid=9861,fd=3))

The process owning the socket was Python.

---

# 16. Testing the HTTP Service

The service was tested with:

curl http://127.0.0.1:8080

The Python HTTP server returned a directory listing.

This confirmed that:

- The service was running.
- TCP port 8080 was listening.
- The local HTTP connection was successful.

---

# 17. nftables Firewall Lab

A temporary nftables table and input chain were created for testing.

The firewall rule:

tcp dport 8080 counter drop

was used to drop TCP traffic destined for port 8080.

The rule was verified with:

nft list ruleset

Example:

table inet labfilter {
    chain input {
        type filter hook input priority filter; policy accept;
        tcp dport 8080 counter packets 3 bytes 180 drop
    }
}

---

# 18. nftables Counter

The counter showed:

packets 3
bytes 180

This means three packets matched the firewall rule, with a total of 180 bytes.

The counter provides useful evidence that traffic actually matched the rule.

---

# 19. Firewall Practical Observation

Before the firewall rule:

Python
  |
0.0.0.0:8080
  |
LISTEN
  |
curl -> successful

After the firewall rule:

Python
  |
0.0.0.0:8080
  |
LISTEN
  |
Firewall
  |
TCP/8080 -> DROP

The Python service remained listening.

The firewall blocked matching traffic.

This demonstrates:

Listening service != Network accessibility

---

# 20. SOC Perspective

A SOC analyst should not investigate a port only by its number.

For a suspicious listening port, investigate:

- Port number
- Protocol
- Process
- PID
- Executable path
- Parent process
- User
- Command line
- Source IP
- Destination IP
- Connection state
- Firewall rules
- Firewall logs
- Timestamp

Example:

0.0.0.0:8080
Process: python3
PID: 9861

An analyst should determine whether the service is expected and whether its network exposure is appropriate.

---

# 21. Important Security Concepts

### Listening port

A service is waiting for network connections.

### Firewall

Controls whether traffic is allowed or blocked.

### Connection state

Describes the current state of a network connection.

### Stateful firewall

Tracks connection state and uses connection context.

### Stateless firewall

Evaluates packets individually according to configured rules.

### Default deny

Blocks traffic unless explicitly permitted.

### Default allow

Allows traffic unless explicitly blocked.

---

# Key Memory

IP Address
-> Identifies the network endpoint/device

Port
-> Identifies the network service/application endpoint

Protocol
-> Defines the communication protocol

Connection State
-> Describes the current state of the connection

Firewall
-> Controls network traffic according to rules

Listening Service
-> Waits for incoming connections

Important:

Listening port != Accessible port
