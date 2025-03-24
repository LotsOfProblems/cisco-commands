# cisco-commands
cheatsheet for ccna commands


# Cisco Commands Cheat Sheet

## 📜 Table of Contents
- [Network Masks](#Network-Masks)
  * [Basic Configuration](#Basic Configuration)
  * [Router on a Stick](#Router-on-a-Stick)
  * [ACL](#ACL)
    + [Simple ACL](#Simple-ACL)
    + [Extended ACL](#Extended-ACL)
  * [OSPF](#OSPF)
  * [Tunnelling](#Tunnelling)
    + [GRE (no encryption)](#gre-no-encryption)
    + [IPSEC](#IPSEC)
  * [QOS](#QOS)
     + [Class Mapping and Marking](#Class-Mapping-and-Marking)
  * [Network Management](#Network-Mangement)
     + [CDP](#CDP)
     + [LLDP](#LLDP)
     + [Logging](#Logging)
     + [Backup](#Backup)
  * [NAT](#NAT)
    
  


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
## Basic Configuration

#### Disable DNS lookup, Hostname, Domain Name, MOTD 
```
enable
config terminal
no ip domain lookup 
hostname R1
ip domain-name ccna-lab.com
banner motd #Unauthorized Access is Prohibited#
```

#### Password
```
line console 0
password ciscoconpass
login
exit

security passwords min-length 10

username admin secret admin1pass

line vty 0 15
login local
transport input ssh
exit

service password-encryption
crypto key generate rsa 
How many bits in the modulus [512]: 1024
```

---
## Router on a Stick
#### Configure VLANs on the Switch
```
enable
configure terminal

! Create VLAN 10
vlan 10
 name VLAN10

! Create VLAN 20
vlan 20
 name VLAN20

! Assign interfaces to VLANs
interface GigabitEthernet0/1
 switchport mode access
 switchport access vlan 10

interface GigabitEthernet0/2
 switchport mode access
 switchport access vlan 20

! Configure Trunking on the link to the Router
interface GigabitEthernet0/3
 switchport mode trunk
 switchport trunk encapsulation dot1q
 switchport trunk allowed vlan 10,20

```
---
#### Configure Router
```
enable
configure terminal

! Configure the trunk interface
interface GigabitEthernet0/0
 no shutdown

! Create Subinterface for VLAN 10
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0

! Create Subinterface for VLAN 20
interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0

```
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
 <summary> Check commands </summary>

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

### QOS
### Class Mapping and Marking
---
```
class-map match-all CRITICAL
 match protocol ospf
class-map match-any MANAGEMENT
 match protocol telnet
 match protocol ssh
class-map match-any WEB
 match protocol http
!
policy-map MARKING
 class CRITICAL
  set precedence 7
 class MANAGEMENT
  set precedence 5
 class WEB
  set precedence 3

interface g0/0/0
service-policy output MARKING
```
---
#### Precedence
| **Value** | **Description**                                    |
|-----------|----------------------------------------------------|
| `0`       | Routine - Match packets with routine precedence   |
| `1`       | Priority - Match packets with priority precedence |
| `2`       | Immediate - Match packets with immediate precedence |
| `3`       | Flash - Match packets with flash precedence       |
| `4`       | Flash Override - Match packets with flash override precedence |
| `5`       | Critical - Match packets with critical precedence |
| `6`       | Internet - Match packets with internetwork control precedence |
| `7`       | Network - Match packets with network control precedence |
---

```
class-map ?
  WORD       class-map name
  match-all  Logical-AND all matching statements under this classmap
  match-any  Logical-OR all matching statements under this classmap
  type       type of the class-map
```
---
```
match ?
  access-group         Access group
  any                  Any packets
  class-map            Class map
  cos                  IEEE 802.1Q/ISL class of service/user priority values
  destination-address  Destination address
  input-interface      Select an input interface to match
  ip                   IP specific values
  not                  Negate this match result
  precedence           Match Precedence in IP(v4) and IPv6 packets
  protocol             Protocol
  qos-group            Qos-group
```

---

## CDP
---

##### Disable (enabled by default)
```
 no cdp enable

```


##### Info
```
show cdp neighbors
show cdp neighbors detail
```


---
## LLDP
---
## Show 
```
show logging
```


##### Enable (disabled by default on terminal console)
```
lldp run
```
###### On interface
```
lldp receive
lldp transmit
```

## Logging

###### Enable
```
logging on
terminal monitor
logging buffered <size> <level>
service timestamps log datetime msec

Levels range from 0 (emergencies) to 7 (debugging):

0 - emergencies
1 - alerts
2 - critical
3 - errors
4 - warnings
5 - notifications
6 - informational
7 - debugging
```


###### Send to external host
```
logging host <IP_Address>
logging host 192.168.1.100
logging trap informational
snmp-server enable traps syslog
```


---

## Backup

---

##### Copy running-config
```
copy running-config tftp:100.10.1.10 R_R-C
```
###### Copy File
```
copy <TFTPpath> tftp:<IPdest> <filename>
```

## NAT
```
# Cisco IOS NAT (Network Address Translation) Commands

## 1. Enable NAT
```sh
configure terminal
```

## 2. Static NAT Configuration
```sh
ip nat inside source static <inside-local-ip> <inside-global-ip>
```
Example:
```sh
ip nat inside source static 192.168.1.10 203.0.113.10
```

## 3. Dynamic NAT Configuration
```sh
ip nat pool NAT_POOL <start-ip> <end-ip> netmask <subnet-mask>
access-list <acl-number> permit <source-ip> <wildcard-mask>
ip nat inside source list <acl-number> pool NAT_POOL
```
Example:
```sh
ip nat pool NAT_POOL 203.0.113.100 203.0.113.200 netmask 255.255.255.0
access-list 1 permit 192.168.1.0 0.0.0.255
ip nat inside source list 1 pool NAT_POOL
```

## 4. Port Address Translation (PAT) / NAT Overload
```sh
ip nat inside source list <acl-number> interface <outside-interface> overload
```
Example:
```sh
access-list 1 permit 192.168.1.0 0.0.0.255
ip nat inside source list 1 interface GigabitEthernet0/0 overload
```

## 5. NAT Interface Configuration
```sh
interface <interface-name>
ip nat inside  # For inside interface
exit
interface <interface-name>
ip nat outside  # For outside interface
exit
```
Example:
```sh
interface GigabitEthernet0/1
 ip nat inside
 exit
interface GigabitEthernet0/0
 ip nat outside
 exit
```

## 6. NAT Verification Commands
```sh
show ip nat translations  # Display NAT translation table
show ip nat statistics  # Display NAT statistics
clear ip nat translation *  # Clear all NAT translations
```

## 7. Remove NAT Configuration
```sh
no ip nat inside source static <inside-local-ip> <inside-global-ip>
no ip nat inside source list <acl-number> pool NAT_POOL
no ip nat inside source list <acl-number> interface <outside-interface> overload
```

## Notes
- **Static NAT**: Maps one inside local IP to one public IP.
- **Dynamic NAT**: Uses a pool of public IPs for internal hosts.
- **PAT (Overload)**: Maps multiple internal IPs to a single public IP using different port numbers.





