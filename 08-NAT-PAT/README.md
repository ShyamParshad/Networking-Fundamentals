# NAT & PAT

## 1. NAT

NAT stands for **Network Address Translation**.

NAT translates IP addresses, commonly between private and public addressing.

Example:

Private IP:
192.168.1.10

Public IP:
49.x.x.x

Basic flow:

Private IP → NAT → Public IP → Internet

Private IP addresses are not directly routable on the public Internet.

---

## 2. PAT

PAT stands for **Port Address Translation**.

PAT uses IP addresses and port numbers to allow multiple private devices to share a single public IP address.

Example:

192.168.1.10:50000 → 49.x.x.x:60001
192.168.1.11:50001 → 49.x.x.x:60002
192.168.1.12:50002 → 49.x.x.x:60003

The different translated ports allow the router to distinguish between connections.

PAT is also commonly called **NAT Overload**.

---

## 3. NAT/PAT Translation Table

A router maintains mappings between internal and public endpoints.

| Inside Local | Inside Global |
|---|---|
| 192.168.1.10:50000 | 49.x.x.x:60001 |
| 192.168.1.11:50001 | 49.x.x.x:60002 |

The mapping allows return traffic to reach the correct internal device.

---

## 4. Full NAT/PAT Packet Journey

Client:

192.168.1.10:50000

Destination:

8.8.8.8:443

The client sends the packet toward its default gateway.

Before NAT:

192.168.1.10:50000 → 8.8.8.8:443

After PAT:

49.x.x.x:60001 → 8.8.8.8:443

The remote server responds to:

49.x.x.x:60001

The router checks its PAT table and translates it back:

49.x.x.x:60001 → 192.168.1.10:50000

The packet is then forwarded to the internal client.

---

## 5. Default Gateway and ARP

If the destination is outside the local network, the host sends the frame toward the **default gateway**.

If the gateway MAC address is unknown, the host uses ARP to discover it.

Example:

192.168.1.1 → AA:BB:CC:DD:EE:FF

Important:

- Destination IP = remote server
- Destination MAC on the local network = default gateway's MAC

---

## 6. Types of NAT

### Static NAT

One private IP maps to one fixed public IP.

192.168.1.10 ↔ 49.x.x.10

### Dynamic NAT

A private IP is temporarily mapped to an available public IP from a pool.

### PAT

Multiple private devices share one public IP using different port numbers.

### Port Forwarding

Incoming traffic on a public IP and port is forwarded to a specific internal IP and port.

Example:

49.x.x.x:8080 → 192.168.1.50:80

---

## 7. NAT vs PAT

NAT:
- Focuses on IP address translation.

PAT:
- Uses IP + port translation.
- Allows multiple private devices to share one public IP.

Memory:

NAT → Address Translation

PAT → Address + Port Translation

---

## 8. Practical Commands

Check IP addresses:

ip addr

Check routing table:

ip route

Check neighbor/ARP information:

ip neigh

Check TCP connections:

ss -ant

Check TCP/UDP listening sockets and processes:

ss -tulnp

Example:

192.168.1.66:41838 → 151.101.1.91:443

192.168.1.66 = local/private IP
41838 = ephemeral source port
151.101.1.91 = remote IP
443 = HTTPS destination port
ESTAB = TCP connection established

---

## 9. SOC Perspective

NAT/PAT is important during network investigation because a public IP may represent multiple internal devices.

To identify the actual internal host behind a public NAT address, analysts may need NAT/PAT translation logs.

A public IP and port alone do not always reveal the exact internal machine.

Unknown or suspicious connections should be investigated using:

- Source IP
- Source port
- Destination IP
- Destination port
- Connection state
- Process
- NAT/PAT logs
- Time of connection
