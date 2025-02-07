# cisco-commands
cheatsheet for ccna commands


# Cisco Commands Cheat Sheet

## 📜 Table of Contents
- [Basic Commands](#basic-commands)
  * [Access Lists ACL](#routing-commands)
    + [Simple ACL](#ACL)
    + [Extended ACL](#Extended-ACL)
  * [OSPF](#OSPF)
    + 
  
- [Security Commands](#security-commands)
- [Troubleshooting](#troubleshooting)

---

#### 
Here are some basic Cisco commands.




## ACL (#basic-networking)


###  Verify ACLs
```sh
show access-lists
show running-config | section access-list
show ip interface GigabitEthernet0/1

show access-lists 101
Extended IP access list 101
  10 permit tcp any any eq 80
  20 permit tcp any any eq 443
  30 deny ip any any
configure terminal
ip access-list extended 101
 no 10   ! Removes rule number 10 (HTTP)
exit
```



 
### Simple ACL
```sh
access-list 100 permit tcp 192.168.1.0 0.0.0.255 10.0.0.0 0.0.0.255
access-list 100 deny ip any any
exit

interface GigabitEthernet0/2
ip access-group 100 in   
exit
```
ACLs can be assigned both **in** and **out** of interfaces, based on when the packets should be verified and discarded or let through.

### Extended ACL
```sh
access-list 110 permit tcp 192.168.1.0 0.0.0.255 10.0.0.0 0.0.0.255 eq 80
access-list 110 permit ip any any

access-list NOPING deny icmp any host 192.168.1.1
```
<details>
##  <summary>Parameters</summary>
 
###  ACL Rules Table

| ACL Number | Action  | Protocol | Source IP      | Destination IP  | Port  |
|-----------|--------|---------|---------------|---------------|------|
| 100       | Permit | TCP     | 192.168.1.0/24 | 10.0.0.0/24  | 80   |
| 101       | Deny   | ICMP    | Any           | 10.0.0.1      | -    |
| 102       | Permit | IP      | 192.168.2.0/24 | Any          | -    |

###  Cisco Extended ACL Structure
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

###  Cisco ACL Operators Table
| Operator  | Description                           | Example Usage                    |
|-----------|--------------------------------------|----------------------------------|
| `eq`      | Matches **exactly** a specific port | `access-list 100 permit tcp any any eq 80` (Allow HTTP) |
| `gt`      | Matches **greater than** a port     | `access-list 101 deny tcp any any gt 1023` (Block dynamic ports) |
| `lt`      | Matches **less than** a port        | `access-list 102 permit udp any any lt 1024` (Allow privileged ports) |
| `range`   | Matches **a range of ports**        | `access-list 103 permit tcp any any range 20 21` (Allow FTP Data & Control) |
| `host`    | Matches **a single IP address**     | `access-list 104 deny ip host 192.168.1.1 any` (Block traffic from a single IP) |
| `any`     | Matches **any IP address**          | `access-list 105 permit ip any any` (Allow all traffic) |
| `log`     | Logs the packet match to syslog     | `access-list 106 deny ip any any log` (Log all denied traffic) |
</details>






---
## OSPF 

#### Verify OSPF Configuration

| Command                 | Description                                   |
|-------------------------|-----------------------------------------------|
| `show ip ospf`         | Displays OSPF settings                        |
| `show ip ospf neighbor` | Shows OSPF neighbors and their states        |
| `show ip ospf database` | Displays OSPF LSAs and routing information   |
| `show ip route ospf`    | Shows OSPF-learned routes                    |
| `debug ip ospf events`  | Debugs OSPF events in real time              |

#### Seting it up

```
router ospf 100
network 192.168.1.0 0.0.0.255 area 0
network 10.0.0.0 0.0.0.255 area 0
router-id 1.1.1.1

interface GigabitEthernet0/0
ip ospf priority 100
exit
```

#### Adjust OSPF Timers
```
interface GigabitEthernet0/0
ip ospf hello-interval 10
ip ospf dead-interval 40
exit
```
#### Enable OSPF Authentication (Optional)
```
interface GigabitEthernet0/0
ip ospf authentication message-digest
ip ospf message-digest-key 1 md5 STRONGPASSWORD
```






