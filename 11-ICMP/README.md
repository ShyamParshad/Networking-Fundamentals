Create a clean Markdown file named ICMP.md for my GitHub repository "Networking-Fundamentals".

# ICMP (Internet Control Message Protocol)

## What is ICMP?

ICMP is a Layer 3 protocol used by network devices and hosts to send control, diagnostic, and error messages.

ICMP is commonly used for:
- Network reachability testing
- Error reporting
- TTL expiration
- Network troubleshooting

ICMP does NOT use TCP/UDP ports.

## ICMP and Ping

`ping` commonly uses ICMP Echo messages.

### Echo Request
- ICMP Type: 8
- Sent by the source to test reachability.

### Echo Reply
- ICMP Type: 0
- Sent back by the destination as a response.

### Packet Flow

Kali
  |
  | ICMP Echo Request (Type 8)
  v
Gateway
  |
  | ICMP Echo Reply (Type 0)
  v
Kali

## ICMP Message Types

| Type | Message | Purpose |
|------|---------|---------|
| 0 | Echo Reply | Response to Echo Request |
| 3 | Destination Unreachable | Delivery problem |
| 8 | Echo Request | Reachability test |
| 11 | Time Exceeded | TTL expired |

### Important Type 3 Codes

| Type | Code | Meaning |
|------|------|---------|
| 3 | 0 | Network Unreachable |
| 3 | 1 | Host Unreachable |
| 3 | 3 | Port Unreachable |

Important:
- Type = broad category
- Code = specific reason

## ICMP Port Unreachable

ICMP itself does not use TCP/UDP ports.

However, an ICMP error can report that a TCP/UDP-related destination was unreachable.

Example:

UDP packet:
127.0.0.1:40069 -> 127.0.0.1:9999

If no UDP service is listening on port 9999, the system can generate:

ICMP Type 3
Code 3
Port Unreachable

### Flow

UDP packet
    |
    v
Destination UDP port 9999
    |
    v
No UDP service
    |
    v
ICMP Type 3 / Code 3
    |
    v
Port Unreachable

## TTL and ICMP

TTL (Time To Live) prevents IP packets from looping indefinitely.

Routers decrement TTL as packets are forwarded.

Example:

TTL = 3
  |
Router
TTL = 2
  |
Router
TTL = 1
  |
Router
TTL = 0
  |
Packet discarded
  |
ICMP Type 11
Time Exceeded

## Traceroute

Traceroute uses controlled TTL values to discover network hops.

Example:

TTL = 1 -> First router
TTL = 2 -> Second router
TTL = 3 -> Third router

When TTL expires at an intermediate router, the router may send an ICMP Time Exceeded message.

Traceroute can use different probe mechanisms depending on implementation and options, so traceroute is not simply "an ICMP command."

## Practical Lab 1 — Ping

Command:

```bash
ping -c 4 192.168.1.254
```

Purpose:
Test ICMP reachability to the gateway.

Observed:

4 packets transmitted
4 packets received
0% packet loss
Practical Lab 2 — Traceroute

Command:

traceroute -n 192.168.1.254

Observed:

1  192.168.1.254  2.616 ms  2.451 ms  2.576 ms

The gateway was the first responding hop.

Practical Lab 3 — ICMP Packet Capture

Command:

sudo tcpdump -i eth0 -n icmp

Then:

ping -c 4 192.168.1.254

Observed packet flow:

192.168.1.71 -> 192.168.1.254
ICMP Echo Request

192.168.1.254 -> 192.168.1.71
ICMP Echo Reply

The capture showed four Echo Requests and four Echo Replies.

This provided packet-level evidence of successful ICMP communication.

Practical Lab 4 — ICMP Port Unreachable

First checked UDP listeners:

ss -uln

No UDP listening sockets were present.

Started ICMP capture:

sudo tcpdump -i lo -n -vv icmp

Generated local UDP traffic:

echo "TEST" | nc -u -w 1 127.0.0.1 9999

Observed:

127.0.0.1 > 127.0.0.1:
ICMP 127.0.0.1 udp port 9999 unreachable

The capture also showed the original UDP packet:

127.0.0.1:40069 -> 127.0.0.1:9999

Interpretation:

UDP destination port 9999
        |
        v
No UDP service
        |
        v
ICMP Type 3 / Code 3
        |
        v
Port Unreachable
SOC Analyst Perspective

A single ICMP error does not automatically indicate an attack.

SOC analysts should investigate:

Source IP
Destination IP
ICMP Type
ICMP Code
Timestamp
Frequency
Destination ports
Repeated patterns
Related firewall logs
Endpoint/network telemetry

Example:

One Port Unreachable event:
Potentially normal.

Hundreds of Port Unreachable events across many ports:
Potentially suspicious and worth investigating.

Key principle:

One event = evidence.

Repeated events + context = potentially suspicious pattern.

Key Takeaways
ICMP operates at Layer 3.
ICMP does not use TCP/UDP ports.
Type 8 = Echo Request.
Type 0 = Echo Reply.
Type 3 = Destination Unreachable.
Type 3 Code 3 = Port Unreachable.
Type 11 = Time Exceeded.
TTL helps prevent routing loops.
TTL expiration can produce ICMP Time Exceeded messages.
Traceroute uses TTL behavior to discover network hops.
Packet capture provides deeper evidence than simply looking at ping output.
SOC analysts investigate patterns and context rather than treating every ICMP error as malicious.
