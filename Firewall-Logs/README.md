# Firewall Logs — SOC Fundamentals

## 1. What Are Firewall Logs?

A firewall log is a record of network traffic that a firewall evaluated and the decision it made.

A firewall rule decides:

* `ALLOW`
* `DROP`
* `REJECT`

A firewall log provides evidence about that decision.

Example:

```text
Time: 14:10:32
Source IP: 192.168.1.50
Destination IP: 192.168.1.66
Source Port: 54321
Destination Port: 8080
Protocol: TCP
Action: DROP
```

A SOC analyst can use this information to investigate network activity.

---

## 2. Important Firewall Log Fields

| Field            | Meaning                                           |
| ---------------- | ------------------------------------------------- |
| Timestamp        | When the event occurred                           |
| Source IP        | System initiating the traffic                     |
| Source Port      | Port used by the source                           |
| Destination IP   | System receiving the traffic                      |
| Destination Port | Service/application port being targeted           |
| Protocol         | TCP, UDP, ICMP, etc.                              |
| Action           | ALLOW, DROP, REJECT                               |
| Connection State | NEW, ESTABLISHED, etc., depending on the firewall |

### SOC Questions

A useful way to read a firewall event:

```text
WHO?
→ Source IP

TO WHOM?
→ Destination IP

WHICH SERVICE?
→ Destination Port

HOW?
→ Protocol

WHEN?
→ Timestamp

WHAT HAPPENED?
→ ALLOW / DROP / REJECT
```

---

## 3. ALLOW vs DROP vs REJECT

### ALLOW

The firewall permitted the traffic.

```text
ACTION=ALLOW
```

Important:

> ALLOW does not automatically mean the traffic is safe.

A malicious connection can also be allowed.

### DROP

The firewall silently discards the traffic.

```text
ACTION=DROP
```

The sender generally does not receive an explicit rejection from the firewall.

### REJECT

The firewall actively refuses the traffic, depending on the protocol and configured rule.

```text
ACTION=REJECT
```

Simple distinction:

```text
DROP
→ silently discard

REJECT
→ actively refuse
```

---

## 4. Single Event vs Suspicious Pattern

A single blocked connection does not automatically indicate an attack.

Example:

```text
10.10.10.50 → 10.10.10.20:22 → DROP
```

Possible explanations include:

* Misconfiguration
* Legitimate user mistake
* Administrative activity
* Monitoring activity
* Unauthorized activity

SOC analysts look for patterns.

Example:

```text
10:15:01 → DROP → TCP/22
10:15:02 → DROP → TCP/22
10:15:03 → DROP → TCP/22
10:15:04 → DROP → TCP/22
10:15:05 → DROP → TCP/22
```

Repeated attempts from the same source against the same destination/service are more interesting and require investigation.

---

## 5. Source and Destination Analysis

Consider:

```text
192.168.1.50:54321 → 192.168.1.66:8080
```

### Source

```text
192.168.1.50:54321
```

* `192.168.1.50` = source IP
* `54321` = source port

The source port may be an ephemeral port.

### Destination

```text
192.168.1.66:8080
```

* `192.168.1.66` = destination IP
* `8080` = destination port

The destination port helps identify the service being targeted.

---

## 6. Firewall Logs and Listening Services

A firewall log showing:

```text
DPORT=8080
ACTION=DROP
```

does **not** necessarily mean that port 8080 is closed.

A service can still be listening while the firewall blocks access to it.

Important distinction:

```text
LISTENING PORT
≠
ACCESSIBLE PORT
```

The application and firewall perform different roles.

Example:

```text
Python HTTP Server
127.0.0.1:8080
      ↓
    LISTEN
      ↓
   Firewall
      ↓
    DROP
```

The application can remain running while traffic is blocked.

---

## 7. Practical Lab — nftables

A temporary nftables lab was created using:

```bash
sudo nft add table inet labfilter
```

A filtering chain was created:

```bash
sudo nft 'add chain inet labfilter input { type filter hook input priority 0; policy accept; }'
```

A temporary rule was added:

```bash
sudo nft add rule inet labfilter input tcp dport 8080 counter drop
```

The resulting ruleset showed:

```text
table inet labfilter {
        chain input {
                type filter hook input priority filter; policy accept;
                tcp dport 8080 counter packets 0 bytes 0 drop
        }
}
```

A local Python HTTP server was running on:

```text
127.0.0.1:8080
```

The server was verified with:

```bash
ss -tulnp
```

A request was then generated:

```bash
curl --max-time 3 http://127.0.0.1:8080
```

The request timed out:

```text
curl: (28) Connection timed out after 3003 milliseconds
```

The nftables counter changed to:

```text
tcp dport 8080 counter packets 3 bytes 180 drop
```

This demonstrated that matching traffic was reaching the firewall rule and being dropped.

The Python service was still listening:

```text
tcp LISTEN 127.0.0.1:8080
```

Therefore:

```text
Service = LISTENING
Firewall = DROPPING TRAFFIC
```

This experimentally demonstrated:

> A listening service does not automatically mean that traffic can successfully reach it.

---

## 8. Firewall Logs as SOC Evidence

Firewall logs become more useful when combined with other security telemetry.

Example:

```text
Firewall:
10.10.10.50 → 10.10.10.20:22 → ALLOW
```

Then an authentication log shows:

```text
Failed SSH login
Source: 10.10.10.50
User: root
```

Multiple failed authentication events following the network connection provide stronger investigative context.

This is called **log correlation**.

SOC analysts may correlate:

* Firewall logs
* Authentication logs
* Endpoint logs
* DNS logs
* Network traffic
* Process activity

---

## 9. SOC Investigation Mindset

Do not immediately conclude:

```text
DROP = Attack
```

Instead ask:

```text
WHO?
→ Source IP

TARGET?
→ Destination IP

SERVICE?
→ Destination Port

WHEN?
→ Timestamp

PROTOCOL?
→ TCP/UDP/etc.

ACTION?
→ ALLOW/DROP/REJECT

PATTERN?
→ Single event or repeated activity?

CONTEXT?
→ What do other logs show?
```

The goal is to turn raw firewall events into an evidence-based conclusion.

---

## Key Takeaways

1. Firewall rules make traffic decisions.
2. Firewall logs provide evidence of those decisions.
3. Source IP identifies where traffic originated.
4. Destination IP identifies the target system.
5. Destination port identifies the targeted service/port.
6. Source ports are often ephemeral for client connections.
7. `DROP` and `REJECT` are not the same.
8. A single blocked event does not automatically mean an attack.
9. Repeated activity can reveal suspicious patterns.
10. A service can be listening while firewall rules prevent access.
11. SOC analysts correlate firewall logs with other telemetry.
12. Investigation requires context, not assumptions.

## SOC Mental Model

```text
RAW FIREWALL EVENT
        ↓
WHO?
        ↓
TARGET?
        ↓
SERVICE?
        ↓
WHEN?
        ↓
ACTION?
        ↓
PATTERN?
        ↓
CORRELATE
        ↓
INVESTIGATE
        ↓
CONCLUSION
```
