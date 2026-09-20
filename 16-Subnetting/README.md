# Subnetting

## 1. What is Subnetting?

Subnetting is the process of dividing an IP network into smaller logical networks called subnets.

Subnetting helps organize networks, control address ranges, and determine whether two IP addresses belong to the same network.

Example:

```text
192.168.1.0/24
        ↓
Can be divided into smaller subnets
        ↓
192.168.1.0/25
192.168.1.128/25
```

---

## 2. CIDR Notation

CIDR stands for **Classless Inter-Domain Routing**.

Example:

```text
192.168.1.71/24
```

The `/24` is the prefix length.

IPv4 has 32 bits in total.

For `/24`:

```text
24 bits → Network portion
8 bits  → Host portion
```

So:

```text
Network Portion       Host Portion
<---------------->   <---------->
192.168.1             .71
```

---

## 3. Subnet Mask

A CIDR prefix can also be represented using a subnet mask.

Example:

```text
/24 = 255.255.255.0
```

Binary representation:

```text
11111111.11111111.11111111.00000000
```

In the subnet mask:

```text
1 → Network bits
0 → Host bits
```

Therefore:

```text
/24
=
255.255.255.0
```

---

## 4. Network Portion and Host Portion

Example:

```text
192.168.1.71/24
```

The `/24` means:

```text
192.168.1
   ↓
Network portion

71
↓
Host portion
```

The host portion changes to identify different hosts inside the same subnet.

---

## 5. Network Address

The network address identifies the subnet itself.

For:

```text
192.168.1.0/24
```

the network address is:

```text
192.168.1.0
```

It is used to identify the network and is not assigned to a normal host in the traditional IPv4 subnetting model.

---

## 6. Broadcast Address

The broadcast address is the last address in the subnet.

For:

```text
192.168.1.0/24
```

the broadcast address is:

```text
192.168.1.255
```

It is used for broadcast communication to hosts within that subnet.

---

## 7. Usable Host Range

For:

```text
192.168.1.0/24
```

the traditional host range is:

```text
Network Address
192.168.1.0

Usable Hosts
192.168.1.1 - 192.168.1.254

Broadcast
192.168.1.255
```

There are:

```text
256 total addresses
```

and traditionally:

```text
256 - 2 = 254 usable host addresses
```

The two excluded addresses are the network address and broadcast address.

---

# 8. /25 Subnetting

For:

```text
192.168.1.0/25
```

Host bits:

```text
32 - 25 = 7
```

Total addresses:

```text
2^7 = 128
```

Traditionally usable:

```text
128 - 2 = 126
```

The `/24` network is divided into two `/25` subnets.

### First subnet

```text
Network   = 192.168.1.0
Hosts     = 192.168.1.1 - 192.168.1.126
Broadcast = 192.168.1.127
```

### Second subnet

```text
Network   = 192.168.1.128
Hosts     = 192.168.1.129 - 192.168.1.254
Broadcast = 192.168.1.255
```

---

# 9. Block Size

Block size is useful for quickly finding subnet ranges.

For common prefixes:

```text
/24 → 256
/25 → 128
/26 → 64
/27 → 32
/28 → 16
/29 → 8
/30 → 4
```

As the prefix length increases:

```text
Prefix increases
      ↓
Subnet becomes smaller
      ↓
Number of hosts per subnet decreases
```

---

# 10. /26 Practical Example

Example:

```text
192.168.1.71/26
```

`/26` has:

```text
32 - 26 = 6 host bits
```

Total addresses:

```text
2^6 = 64
```

Block size:

```text
64
```

Ranges:

```text
0 - 63
64 - 127
128 - 191
192 - 255
```

The IP `71` falls into:

```text
64 - 127
```

Therefore:

```text
Network   = 192.168.1.64
Hosts     = 192.168.1.65 - 192.168.1.126
Broadcast = 192.168.1.127
```

---

# 11. /27 Practical Example

Example:

```text
192.168.1.71/27
```

Host bits:

```text
32 - 27 = 5
```

Total addresses:

```text
2^5 = 32
```

Block size:

```text
32
```

Ranges:

```text
0 - 31
32 - 63
64 - 95
96 - 127
128 - 159
160 - 191
192 - 223
224 - 255
```

The IP `71` falls into:

```text
64 - 95
```

Therefore:

```text
Network   = 192.168.1.64
Hosts     = 192.168.1.65 - 192.168.1.94
Broadcast = 192.168.1.95
```

---

# 12. Another /27 Example

Example:

```text
192.168.1.200/27
```

Block size:

```text
32
```

Ranges:

```text
0 - 31
32 - 63
64 - 95
96 - 127
128 - 159
160 - 191
192 - 223
224 - 255
```

The IP `200` falls into:

```text
192 - 223
```

Therefore:

```text
Network   = 192.168.1.192
Hosts     = 192.168.1.193 - 192.168.1.222
Broadcast = 192.168.1.223
```

---

# 13. Subnetting in a Different Octet

Subnetting does not always happen in the last octet.

Example:

```text
172.16.35.10/20
```

The subnet mask is:

```text
/20 = 255.255.240.0
```

The important octet is the third octet:

```text
255.255.240.0
        ↑
     3rd octet
```

Block size:

```text
256 - 240 = 16
```

Third-octet ranges:

```text
0 - 15
16 - 31
32 - 47
48 - 63
64 - 79
80 - 95
...
```

The third octet is `35`.

Therefore:

```text
35 → 32 - 47
```

So:

```text
Network   = 172.16.32.0
Hosts     = 172.16.32.1 - 172.16.47.254
Broadcast = 172.16.47.255
```

### Important rule

> Find the octet where the subnet mask is neither `255` nor `0`.

Then:

```text
Block Size = 256 - Subnet Mask Value
```

Examples:

```text
/26 → 255.255.255.192
256 - 192 = 64

/27 → 255.255.255.224
256 - 224 = 32

/20 → 255.255.240.0
256 - 240 = 16
```

---

# 14. Same Subnet vs Different Subnet

Subnetting is also used to determine whether two IP addresses belong to the same subnet.

Example:

```text
IP 1 = 192.168.10.20/27
IP 2 = 192.168.10.50/27
```

`/27` block size:

```text
32
```

Ranges:

```text
0 - 31
32 - 63
64 - 95
...
```

Therefore:

```text
192.168.10.20 → 0 - 31
192.168.10.50 → 32 - 63
```

The hosts are in:

```text
Different subnets
```

Their network addresses are:

```text
IP 1 → 192.168.10.0/27
IP 2 → 192.168.10.32/27
```

---

# 15. Another Same Subnet Example

Example:

```text
IP 1 = 192.168.1.20/27
IP 2 = 192.168.1.25/27
```

Both IPs fall in:

```text
0 - 31
```

Therefore:

```text
Same subnet
```

---

# 16. Subnetting + Routing + ARP

Subnetting determines whether the destination is on the same local subnet or a different subnet.

### Same Subnet

Example:

```text
Host A = 192.168.1.20/27
Host B = 192.168.1.25/27
```

Both are in the same subnet.

Host A can directly communicate with Host B.

ARP is used to find the destination host's MAC address:

```text
Host A
  ↓
ARP for Host B
  ↓
Host B MAC
  ↓
Ethernet Frame
  ↓
Host B
```

### Different Subnet

Example:

```text
Host A = 192.168.1.20/27
Host B = 192.168.1.50/27
```

These are different subnets.

Host A should send the packet toward the gateway/next hop.

ARP is used to find the gateway's MAC address:

```text
Host A
  ↓
ARP for Gateway
  ↓
Gateway MAC
  ↓
Router
  ↓
Other Subnet
  ↓
Host B
```

### Important Rule

> Same subnet → ARP for the destination host's MAC.

> Different subnet → ARP for the next-hop/gateway MAC.

This connects subnetting with the previously learned Routing and ARP concepts.

---

# 17. SOC-Relevant Example

Suppose a security alert shows:

```text
Source IP      = 192.168.1.20
Destination IP = 192.168.1.50
Subnet         = /27
```

Using `/27`:

```text
Block size = 32

192.168.1.20 → 0 - 31
192.168.1.50 → 32 - 63
```

Therefore:

```text
Different subnets
```

The traffic should use a router/next hop rather than direct Layer 2 delivery.

This can help during network traffic investigation.

---

# 18. SOC-Style Practical

Network:

```text
192.168.10.0/26
```

Hosts:

```text
Host A = 192.168.10.10
Host B = 192.168.10.50
Host C = 192.168.10.70
Host D = 192.168.10.120
```

`/26` block size:

```text
64
```

Ranges:

```text
0 - 63
64 - 127
128 - 191
192 - 255
```

Therefore:

```text
Host A = 10 → first subnet
Host B = 50 → first subnet

Host C = 70 → second subnet
Host D = 120 → second subnet
```

Results:

```text
A + B → Same subnet
A + C → Different subnet
C + D → Same subnet
```

If Host A wants to communicate with Host C:

```text
Host A
   ↓
Different subnet
   ↓
ARP for Gateway
   ↓
Gateway MAC
   ↓
Router
   ↓
Host C subnet
```

---

# 19. Fast Subnetting Method

For basic subnet calculations:

```text
1. Read CIDR
      ↓
2. Convert to subnet mask
      ↓
3. Find block size
      ↓
4. Build ranges
      ↓
5. Find the range containing the IP
      ↓
6. Network address = first address of the range
      ↓
7. Broadcast = last address of the range
      ↓
8. Usable hosts = addresses between network and broadcast
```

Example:

```text
192.168.1.200/27
```

Process:

```text
/27
↓
Block size = 32
↓
Ranges:
0-31
32-63
64-95
96-127
128-159
160-191
192-223
224-255
↓
200 → 192-223
↓
Network = 192.168.1.192
Broadcast = 192.168.1.223
Hosts = 192.168.1.193-192.168.1.222
```

---

# 20. Key Takeaways

- IPv4 has 32 bits.
- CIDR notation such as `/24`, `/25`, `/26`, and `/27` defines the network prefix length.
- A subnet mask separates network bits from host bits.
- Network address identifies the subnet.
- Broadcast address is the last address in the subnet.
- Usable hosts are traditionally the addresses between the network and broadcast addresses.
- Larger prefix length means a smaller subnet and fewer host addresses.
- Block size helps quickly calculate subnet ranges.
- Subnetting can occur in different octets depending on the prefix.
- Two IPs in the same subnet can communicate directly at Layer 2.
- Two IPs in different subnets require a router/next hop for Layer 3 forwarding.
- ARP finds the destination MAC for same-subnet communication.
- ARP finds the next-hop/gateway MAC for different-subnet communication.
- Subnetting is useful in network traffic analysis and SOC investigations.

## Final Mental Model

```text
CIDR
 ↓
Subnet Mask
 ↓
Network / Host Portion
 ↓
Block Size
 ↓
Subnet Range
 ↓
Network Address
 ↓
Broadcast Address
 ↓
Host Range
 ↓
Same or Different Subnet
 ↓
Direct Delivery or Gateway
 ↓
ARP + Routing Decision
```
