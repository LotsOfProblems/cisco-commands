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

## Basic Commands
Here are some basic Cisco commands.


---

## Simple ACL

access-list 100 permit tcp 192.168.1.0 0.0.0.255 10.0.0.0 0.0.0.255 
access-list 100 deny ip any any
exit

interface GigabitEthernet0/2
ip access-group 100 in   
exit

ACLs can be assigned both in and out of interfaces, based on when the packets should be verified and sicarded or let through

---
##Extended-ACL

Parameter	Description	Example
**ACL Number**	Identifies the ACL (100-199 & 2000-2699 for extended ACLs)	access-list 100

**permit/deny**	permit allows traffic, deny blocks traffic	permit or deny

**Protocol	Filters** by protocol (IP, TCP, UDP, ICMP, etc.)	tcp, udp, icmp, ip

**Source IP**	Specifies the originating IP address or subnet	192.168.1.0

**Wildcard Mask**	Defines how much of the source IP should be matched	0.0.0.255 (for /24 subnet)

**Destination IP**	Specifies the target IP address or subnet	10.0.0.0

**Destination Wildcard** Mask	Defines how much of the destination IP should be matched	0.0.0.255

**Operator (Optional)**	Defines ports & conditions (eq, gt, lt, range)	eq 80 (HTTP), gt 1024 (Dynamic Ports)

**Port (Optional)**	Defines specific service ports (used with TCP/UDP)	eq 22 (SSH), eq 443 (HTTPS)

Operator	Example
**eq**	Equal to a specific port	eq 80 (HTTP)
**gt**	Greater than a specific port	gt 1023 (Ephemeral Ports)
**lt**	Less than a specific port	lt 1024 (Privileged Ports)
**range**	Defines a range of ports	range 20 21 (FTP Data & Control)

Example

access-list 110 deny icmp any host 192.168.1.1
access-list 110 permit ip any any
exit
//
Add to interface..


---

##Verifiy ACLs

show access-lists
show running-config | section access-list
show ip interface GigabitEthernet0/1



```sh



