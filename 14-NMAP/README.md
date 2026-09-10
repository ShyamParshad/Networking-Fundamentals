# Nmap

## 1. What is Nmap?

Nmap stands for **Network Mapper**.

Nmap is a network scanning tool used to discover hosts, identify open ports, and gather information about network services.

Basic flow:

```text
Target Host
    ↓
Nmap Scan
    ↓
Ports
    ↓
Port State
    ↓
Service / Version Information
```

Nmap can help an analyst understand which network services are reachable on a target system.


## 2. Port Scanning

A port is a logical communication endpoint on a host.

Example:

```text
192.168.1.254:80
192.168.1.254:443
```

Here:

```text
192.168.1.254 = Host
80            = Port
443           = Port
```

Nmap can scan ports and determine their state.


## 3. Important Port States

### Open

An open port means Nmap found a service listening and reachable on that port.

Example:

```text
8080/tcp open
```

Meaning:

> A service is listening on TCP port 8080 and the port is reachable.

### Closed

A closed port means the host is reachable but no service is listening on that port.

Example:

```text
8080/tcp closed
```

Meaning:

```text
Host reachable
Port reachable
Service not listening
```

### Filtered

A filtered port means filtering such as a firewall prevents Nmap from determining whether the port is open or closed.

Example:

```text
22/tcp filtered
```

Important rule:

```text
open     → service is reachable/listening
closed   → host reachable but no service listening
filtered → filtering prevents a clear determination
```


## 4. Basic Nmap Scan

A basic scan was performed against the local Kali machine:

```bash
nmap 127.0.0.1
```

`127.0.0.1` is the loopback address and refers to the local machine.

Observed result:

```text
Host is up
All 1000 scanned ports on localhost are in ignored states
Not shown: 1000 closed tcp ports
```

Interpretation:

```text
Host       → UP
Ports      → 1000 scanned
Open ports → 0 detected
Closed     → 1000
```

This demonstrated that the host was reachable but no open ports were found among the default scanned TCP ports.


## 5. Scanning a Specific Port

A specific port can be scanned using the `-p` option.

Example:

```bash
nmap 127.0.0.1 -p 8080
```

Initially, when no service was listening on port 8080, it was closed.

A temporary Python HTTP server was then started:

```bash
python3 -m http.server 8080 --bind 127.0.0.1
```

After starting the service, the scan returned:

```text
PORT     STATE SERVICE
8080/tcp open  http-proxy
```

Interpretation:

```text
8080/tcp
→ TCP port 8080

open
→ Service is listening and reachable

http-proxy
→ Nmap-associated service name
```

The service name shown by Nmap is an identification result and should not automatically be treated as absolute proof of the exact application.


## 6. Service and Version Detection

Nmap can attempt to identify the service and version using `-sV`.

Command:

```bash
nmap -sV 127.0.0.1 -p 8080
```

Observed result:

```text
PORT     STATE SERVICE VERSION
8080/tcp open  http    SimpleHTTPServer 0.6 (Python 3.13.3)
```

Interpretation:

```text
8080/tcp
→ Port 8080 using TCP

open
→ Port is open

http
→ Nmap identified HTTP

SimpleHTTPServer 0.6
→ Identified implementation

Python 3.13.3
→ Detected Python version information
```

`-sV` performs **service and version detection**.


## 7. Common Nmap Commands

### Basic scan

```bash
nmap 127.0.0.1
```

Scans the default set of common TCP ports.

### Specific port

```bash
nmap 127.0.0.1 -p 8080
```

Scans only port 8080.

### Multiple ports

```bash
nmap 127.0.0.1 -p 22,80,443
```

Scans only ports 22, 80, and 443.

### Service and version detection

```bash
nmap -sV 127.0.0.1 -p 8080
```

Attempts to identify the service and version running on the selected port.


## 8. Practical Workflow

The practical workflow used during learning was:

```text
1. Scan localhost
       ↓
2. Check port states
       ↓
3. Start a temporary local service
       ↓
4. Scan the specific port
       ↓
5. Confirm the port changed from closed to open
       ↓
6. Use -sV for service/version detection
```

Example:

```text
No service
    ↓
8080/tcp closed

Python HTTP server started
    ↓
8080/tcp open

-sV scan
    ↓
HTTP
SimpleHTTPServer 0.6
Python 3.13.3
```


## 9. SOC Analyst Relevance

Nmap provides useful network visibility for security monitoring and investigation.

An analyst may use scan results to understand:

- Which ports are reachable
- Which services are exposed
- Which services are expected
- Which services may need investigation
- What service/version information is available

Example:

```text
22/tcp   open ssh
443/tcp  open https
8080/tcp open http
```

A SOC analyst should ask:

> Are these services expected on this system?

An unexpected open port does not automatically mean an attack. It should be investigated in context.


## 10. Important Security Principle

Nmap provides **information and visibility**.

A scan result by itself does not prove that a system is malicious or compromised.

Example:

```text
8080/tcp open
```

This only tells us that the port is open and a service is reachable.

The next step in a real investigation would be to understand:

```text
What service is running?
Why is it running?
Is it expected?
Who started it?
Is it exposed intentionally?
```

Always perform scanning only against systems you own or are explicitly authorized to test.


## 11. Key Takeaways

- Nmap stands for Network Mapper.
- Nmap is used for network and port discovery.
- `open` means a service is reachable/listening.
- `closed` means the host is reachable but no service is listening on that port.
- `filtered` means filtering prevents a clear determination.
- `-p` is used to specify ports.
- `-sV` performs service and version detection.
- `127.0.0.1` can be safely used as a local lab target.
- Nmap results provide visibility that can support security investigation.
- A port being open does not automatically mean the system is compromised.
