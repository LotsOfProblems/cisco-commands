# cisco-commands
cheatsheet for ccna commands


# Cisco Commands Cheat Sheet

## 📜 Table of Contents
- [Network Masks](#Network-Masks)
  * [ACL](#ACL)
    + [Simple ACL](#Simple-ACL)
    + [Extended ACL](#Extended-ACL)
  * [OSPF](#OSPF)
    + [Setting it up](#Setting-it-up)
  * [Tunnelling](#Tunnelling)
    + [GRE (no encryption)](#gre-no-encryption)
    + [IPSEC](#IPSEC)
  
- [Security Commands](#security-commands)
- [Troubleshooting](#troubleshooting)

---

 ## Network Masks
<details>
 

 
<summary> Table </summary>


| Subnet Mask       | CIDR Notation | Wildcard Mask  | Total Hosts | Usable Hosts |
|-------------------|--------------|---------------|------------|--------------|
| 255.0.0.0        | /8           | 0.255.255.255 | 16,777,216 | 16,777,214   |
| 255.128.0.0      | /9           | 0.127.255.255 | 8,388,608  | 8,388,606    |
| 255.192.0.0      | /10          | 0.63.255.255  | 4,194,304  | 4,194,302    |
| 255.224.0.0      | /11          | 0.31.255.255  | 2,097,152  | 2,097,150    |
| 255.240.0.0      | /12          | 0.15.255.255  | 1,048,576  | 1,048,574    |
| 255.248.0.0      | /13          | 0.7.255.255   | 524,288    | 524,286      |
| 255.252.0.0      | /14          | 0.3.255.255   | 262,144    | 262,142      |
| 255.254.0.0      | /15          | 0.1.255.255   | 131,072    | 131,070      |
| 255.255.0.0      | /16          | 0.0.255.255   | 65,536     | 65,534       |
| 255.255.128.0    | /17          | 0.0.127.255   | 32,768     | 32,766       |
| 255.255.192.0    | /18          | 0.0.63.255    | 16,384     | 16,382       |
| 255.255.224.0    | /19          | 0.0.31.255    | 8,192      | 8,190        |
| 255.255.240.0    | /20          | 0.0.15.255    | 4,096      | 4,094        |
| 255.255.248.0    | /21          | 0.0.7.255     | 2,048      | 2,046        |
| 255.255.252.0    | /22          | 0.0.3.255     | 1,024      | 1,022        |
| 255.255.254.0    | /23          | 0.0.1.255     | 512        | 510          |
| 255.255.255.0    | /24          | 0.0.0.255     | 256        | 254          |
| 255.255.255.128  | /25          | 0.0.0.127     | 128        | 126          |
| 255.255.255.192  | /26          | 0.0.0.63      | 64         | 62           |
| 255.255.255.224  | /27          | 0.0.0.31      | 32         | 30           |
| 255.255.255.240  | /28          | 0.0.0.15      | 16         | 14           |
| 255.255.255.248  | /29          | 0.0.0.7       | 8          | 6            |
| 255.255.255.252  | /30          | 0.0.0.3       | 4          | 2            |
| 255.255.255.254  | /31          | 0.0.0.1       | 2          | 2 (Point-to-Point) |
| 255.255.255.255  | /32          | 0.0.0.0       | 1          | 1 (Host Only) |

</details>

---



## ACL 


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

#### Setting it up 

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
## Tunnelling

#### GRE (No Encryption)

## GRE Over IPsec Verification Commands

<details>
 <summary> Table </summary>

| Command                  | Purpose                                      |
|--------------------------|----------------------------------------------|
| `show interfaces Tunnel0` | Check if the GRE tunnel is up              |
| `show crypto ipsec sa`    | Ensure GRE traffic is encrypted            |
| `show crypto isakmp sa`   | Verify IPsec negotiation                   |
| `show ip ospf neighbor`   | Check if OSPF adjacency is formed          |
| `traceroute 10.0.0.2`     | Confirm traffic follows the GRE tunnel     |

| Command                  | Purpose                                      |
|--------------------------|----------------------------------------------|
| `show interfaces Tunnel0` | Check if the GRE tunnel interface is up    |
| `show ip interface brief` | Verify the tunnel has an assigned IP       |
| `show ip route`          | Confirm the tunnel is being used for routing |
| `show ip ospf neighbor`   | Check if OSPF adjacency is formed          |
| `debug tunnel`           | Show real-time GRE tunnel operations        |
| `traceroute 10.0.0.2`     | Confirm traffic follows the GRE tunnel     |

</details>


###### Endpoint 1
```

interface Tunnel0
 ip address 10.0.0.1 255.255.255.252  ! Assign Tunnel IP
 tunnel source 192.168.1.1            ! Local physical IP
 tunnel destination 203.0.113.1        ! Remote physical IP

```
###### Endpoint 2
```

interface Tunnel0
 ip address 10.0.0.2 255.255.255.252  ! Assign Tunnel IP
 tunnel source 203.0.113.1            ! Local physical IP
 tunnel destination 192.168.1.1        ! Remote physical IP

```
###### Enable OSPF routing on both
```
router ospf 1
 network 10.0.0.0 0.0.0.3 area 0
 network 192.168.1.0 0.0.0.255 area 0
```

## IPSEC

###### with GRE or not
##### Configure ISAKMP (IKE) Phase 1
###### Both Endpoints
```
crypto isakmp policy 10
 encryption aes 256
 hash sha
 authentication pre-share
 group 5
 lifetime 86400
exit

crypto isakmp key SECRET-KEY address 203.0.113.1
```
##### Configure IPsec Transform Set (Encryption)
###### Both Endpoints
```
configure terminal
crypto ipsec transform-set VPN-SET esp-aes esp-sha-hmac
 mode tunnel
exit

```
##### Create Crypto Map and apply Transform Set
###### Both Endpoints
Keep in mind ACL number
```
crypto map VPN-MAP 10 ipsec-isakmp
 set peer 203.0.113.1
 set transform-set VPN-SET
 match address 110
exit
```

##### Define an ACL for Traffic Encryption
###### Both Endpoints
```
access-list 110 permit gre host 192.168.1.1 host 203.0.113.1
exit

```

##### Apply the Crypto Map to the WAN Interface
###### Both Endpoints
```
interface GigabitEthernet0/0
 crypto map VPN-MAP
exit

```
