# cisco-commands
cheatsheet for ccna commands


# Cisco Commands Cheat Sheet

## 📜 Table of Contents
- [Basic Commands](#basic-commands)
  *[Access Lists ACL](#routing-commands)
    + [Simple ACL](#Simple-ACL)
    + [Extended ACL](#Extended-ACL)
- [Setting up OSPF](#switching-commands)
- [Security Commands](#security-commands)
- [Troubleshooting](#troubleshooting)

---

#### Basic Commands
Here are some basic Cisco commands.


---





---
#### Verifiy ACLs

```
show access-lists
show running-config | section access-list
show ip interface GigabitEthernet0/1
```

#### Simple ACL
```
access-list 100 permit tcp 192.168.1.0 0.0.0.255 10.0.0.0 0.0.0.255 
access-list 100 deny ip any any
exit

interface GigabitEthernet0/2
ip access-group 100 in   
exit
```

ACLs can be assigned both in and out of interfaces, based on when the packets should be verified and sicarded or let through

---
#### Extended-ACL

```
access-list 101 permit tcp 192.168.1.0 0.0.0.255 10.0.0.0 0.0.0.255 eq 80
```

####🔹 Cisco Extended ACL Structure

| Component      | Description                                            | Example Value                          |
|---------------|--------------------------------------------------------|----------------------------------------|
| `access-list` | Starts the ACL definition                              | `access-list`                          |
| `ACL Number`  | Identifies the ACL (100-199 or 2000-2699 for Extended ACLs) | `100` |
| `permit/deny` | Defines whether traffic is allowed or blocked         | `permit` / `deny`                      |
| `Protocol`    | Specifies the protocol to filter (IP, TCP, UDP, ICMP)  | `tcp`, `udp`, `icmp`, `ip`             |
| `Source IP`   | Defines the source IP or network                      | `192.168.1.0`                          |
| `Wildcard Mask (Src)` | Wildcard mask for source IP                    | `0.0.0.255` (/24 subnet)               |
| `Destination IP` | Defines the destination IP or network               | `10.0.0.0`                             |
| `Wildcard Mask (Dst)` | Wildcard mask for destination IP               | `0.0.0.255`                            |
| `Operator (Optional)` | Specifies conditions (eq, gt, lt, range)       | `eq 80` (for HTTP)                     |
| `Port (Optional)` | Specifies a port number (used with TCP/UDP)        | `eq 22` (for SSH)                      |
| `log (Optional)` | Logs matched packets to syslog                      | `log`                                  |

```
access-list 110 deny icmp any host 192.168.1.1
access-list 110 permit ip any any
```

#### 🔹 Cisco ACL Operators Table

| Operator  | Description                           | Example Usage                    |
|-----------|--------------------------------------|----------------------------------|
| `eq`      | Matches **exactly** a specific port | `access-list 100 permit tcp any any eq 80` (Allow HTTP) |
| `gt`      | Matches **greater than** a port     | `access-list 101 deny tcp any any gt 1023` (Block dynamic ports) |
| `lt`      | Matches **less than** a port        | `access-list 102 permit udp any any lt 1024` (Allow privileged ports) |
| `range`   | Matches **a range of ports**        | `access-list 103 permit tcp any any range 20 21` (Allow FTP Data & Control) |
| `host`    | Matches **a single IP address**     | `access-list 104 deny ip host 192.168.1.1 any` (Block traffic from a single IP) |
| `any`     | Matches **any IP address**          | `access-list 105 permit ip any any` (Allow all traffic) |
| `log`     | Logs the packet match to syslog     | `access-list 106 deny ip any any log` (Log all denied traffic) |





```sh



