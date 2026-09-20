Absolutely. For tomorrow's lab exam, you don't need every Cisco command. You need the **main commands + exact patterns** for each lab.

# 🔥 ICE-4102 Final Command Cheat Sheet

## 0. The universal router interface setup

You will use this in almost every routing lab.

```text
enable
configure terminal

interface g0/0
ip address <IP> <SUBNET-MASK>
no shutdown
exit

interface g0/1
ip address <IP> <SUBNET-MASK>
no shutdown
exit

end
```

Check:

```text
show ip interface brief
```

You want:

```text
up    up
```

---

# 1. Basic Networking Commands

### From PC

```text
ipconfig
```

Shows IP configuration.

```text
ping <IP>
```

Tests connectivity.

```text
tracert <IP>
```

Shows packet path.

```text
arp -a
```

Shows IP → MAC mappings.

---

### From Router

```text
show ip interface brief
```

Interface status + IP addresses.

```text
show ip route
```

Routing table.

```text
show running-config
```

Current configuration.

```text
show interfaces
```

Detailed interface information.

```text
ping <IP>
```

Test connectivity.

```text
traceroute <IP>
```

Trace route from router.

```text
show cdp neighbors
```

See directly connected Cisco devices.

---

# 2. Wired LAN

Typical:

```text
PC ─ Switch ─ PC
```

PC configuration is done through:

```text
PC → Desktop → IP Configuration
```

Example:

```text
PC0
IP:      192.168.1.2
Mask:    255.255.255.0
Gateway: 192.168.1.1
```

For a pure switch LAN, usually **no router configuration is necessary**.

Test:

```text
ping 192.168.1.3
```

---

# 3. IPv4 Addressing & Subnetting

The commands themselves are mainly interface configuration.

### /24

```text
ip address 192.168.10.1 255.255.255.0
```

### /30

```text
ip address 10.0.12.1 255.255.255.252
```

Remember:

```text
/24 → 255.255.255.0
/30 → 255.255.255.252
```

Check:

```text
show ip interface brief
```

---

# 4. Static Routing ⭐

## Main command

```text
ip route <DESTINATION-NETWORK> <MASK> <NEXT-HOP>
```

Example:

```text
ip route 192.168.20.0 255.255.255.0 10.0.12.2
```

Meaning:

> To reach 192.168.20.0/24, send the packet to 10.0.12.2.

---

### Example

Topology:

```text
PC0 -- R1 -- R2 -- R3 -- PC1
```

R1:

```text
ip route 192.168.20.0 255.255.255.0 10.0.12.2
```

R2:

```text
ip route 192.168.20.0 255.255.255.0 10.0.23.2
ip route 192.168.10.0 255.255.255.0 10.0.12.1
```

R3:

```text
ip route 192.168.10.0 255.255.255.0 10.0.23.1
```

Verify:

```text
show ip route
```

Look for:

```text
S
```

---

# 5. RIP ⭐⭐⭐

## Main configuration

```text
router rip
version 2
no auto-summary
network <NETWORK>
```

Example R1:

```text
router rip
version 2
no auto-summary
network 192.168.10.0
network 10.0.12.0
```

R2:

```text
router rip
version 2
no auto-summary
network 10.0.12.0
network 10.0.23.0
```

R3:

```text
router rip
version 2
no auto-summary
network 10.0.23.0
network 192.168.20.0
```

### Verify

```text
show ip route
```

Look for:

```text
R
```

More specifically:

```text
show ip route rip
```

Check protocol:

```text
show ip protocols
```

Test:

```text
ping <destination-IP>
```

---

# 6. EIGRP ⭐⭐⭐

## Main configuration

All routers must use the **same AS number**.

Example:

```text
router eigrp 100
network 172.16.1.0
network 172.16.12.0
```

R1:

```text
router eigrp 100
network 172.16.1.0
network 172.16.12.0
```

R2:

```text
router eigrp 100
network 172.16.12.0
network 172.16.23.0
```

R3:

```text
router eigrp 100
network 172.16.23.0
network 172.16.3.0
```

### Verify neighbors

```text
show ip eigrp neighbors
```

### Verify routes

```text
show ip route
```

Look for:

```text
D
```

or:

```text
show ip route eigrp
```

### Verify protocol

```text
show ip protocols
```

### Test

```text
ping <destination-IP>
```

---

# 7. OSPF ⭐⭐⭐

This one is slightly different because you use a **wildcard mask**.

## Main configuration

```text
router ospf 1
network <NETWORK> <WILDCARD-MASK> area 0
```

### /24

Subnet:

```text
255.255.255.0
```

Wildcard:

```text
0.0.0.255
```

### /30

Subnet:

```text
255.255.255.252
```

Wildcard:

```text
0.0.0.3
```

---

### R1 example

```text
router ospf 1
network 192.168.1.0 0.0.0.255 area 0
network 10.1.12.0 0.0.0.3 area 0
network 10.1.13.0 0.0.0.3 area 0
```

R2:

```text
router ospf 1
network 10.1.12.0 0.0.0.3 area 0
network 10.1.23.0 0.0.0.3 area 0
```

R3:

```text
router ospf 1
network 10.1.23.0 0.0.0.3 area 0
network 10.1.13.0 0.0.0.3 area 0
network 192.168.3.0 0.0.0.255 area 0
```

### Verify neighbors

```text
show ip ospf neighbor
```

### Verify routes

```text
show ip route
```

Look for:

```text
O
```

Or:

```text
show ip route ospf
```

### Verify protocol

```text
show ip protocols
```

### Test

```text
ping <destination-IP>
```

---

# 8. DHCP ⭐⭐

If the **router itself is the DHCP server**:

## Interface

```text
interface g0/0
ip address 192.168.10.1 255.255.255.0
no shutdown
exit
```

## Exclude addresses

```text
ip dhcp excluded-address 192.168.10.1 192.168.10.10
```

This prevents DHCP from assigning those addresses.

## Create pool

```text
ip dhcp pool LAN
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
dns-server 8.8.8.8
```

Then on PC:

```text
Desktop
→ IP Configuration
→ DHCP
```

### Verify

```text
show ip dhcp binding
```

Shows assigned IPs.

```text
show ip dhcp pool
```

Shows DHCP pool information.

```text
show running-config
```

Check DHCP configuration.

---

# 9. VLAN ⭐⭐⭐

## Create VLAN

```text
enable
configure terminal

vlan 10
name ICE
exit

vlan 20
name CSE
exit

vlan 30
name EECE
exit
```

---

## Assign PC port to VLAN

Example:

```text
interface fa0/1
switchport mode access
switchport access vlan 10
exit
```

For multiple ports:

```text
interface range fa0/1-4
switchport mode access
switchport access vlan 10
exit
```

VLAN 20:

```text
interface range fa0/5-8
switchport mode access
switchport access vlan 20
exit
```

VLAN 30:

```text
interface range fa0/9-12
switchport mode access
switchport access vlan 30
exit
```

---

## Trunk between switches

```text
interface g0/1
switchport mode trunk
exit
```

Optional:

```text
switchport trunk allowed vlan 10,20,30
```

### Verify VLAN

```text
show vlan brief
```

### Verify trunk

```text
show interfaces trunk
```

---

# 10. Troubleshooting ⭐⭐⭐

These are the commands you should remember most.

## Interface

```text
show ip interface brief
```

If:

```text
administratively down
```

fix:

```text
configure terminal
interface g0/0
no shutdown
```

---

## Routing table

```text
show ip route
```

Look for:

```text
C = Connected
S = Static
R = RIP
D = EIGRP
O = OSPF
```

---

## Configuration

```text
show running-config
```

---

## RIP

```text
show ip protocols
show ip route rip
```

---

## EIGRP

```text
show ip eigrp neighbors
show ip route eigrp
```

---

## OSPF

```text
show ip ospf neighbor
show ip route ospf
```

---

## VLAN

```text
show vlan brief
show interfaces trunk
```

---

## DHCP

```text
show ip dhcp binding
show ip dhcp pool
```

---

## Connectivity

```text
ping <IP>
```

---

# 11. Serial connection commands

If your Packet Tracer topology uses **serial interfaces**, you may need this.

On the **DCE side**:

```text
interface serial 0/0/0
ip address 10.0.12.1 255.255.255.252
clock rate 64000
no shutdown
```

Check whether an interface is DCE with:

```text
show controllers serial 0/0/0
```

Usually, for your exam, if you're using GigabitEthernet links, you **don't need `clock rate`**.

---

# 12. Saving configuration

Very useful after completing a lab:

```text
copy running-config startup-config
```

or:

```text
write memory
```

You'll usually see:

```text
Destination filename [startup-config]?
```

Press **Enter**.

---

# 13. Enter/exit commands

These are basic but you'll constantly use them.

```text
enable
```

```text
configure terminal
```

```text
exit
```

Moves back one configuration level.

```text
end
```

Returns directly to:

```text
Router#
```

---

# 🔥 The commands you REALLY need to memorize

If you have very little time, memorize this block:

```text
enable
configure terminal

interface g0/0
ip address <IP> <MASK>
no shutdown
exit

show ip interface brief
show ip route
show running-config
ping <IP>
```

### Static

```text
ip route <NETWORK> <MASK> <NEXT-HOP>
```

### RIP

```text
router rip
version 2
no auto-summary
network <NETWORK>
```

### EIGRP

```text
router eigrp 100
network <NETWORK>
```

### OSPF

```text
router ospf 1
network <NETWORK> <WILDCARD> area 0
```

### DHCP

```text
ip dhcp excluded-address <START> <END>

ip dhcp pool LAN
network <NETWORK> <MASK>
default-router <GATEWAY>
dns-server 8.8.8.8
```

### VLAN

```text
vlan 10
name ICE

interface fa0/1
switchport mode access
switchport access vlan 10
```

### Trunk

```text
interface g0/1
switchport mode trunk
```

---

# 🧠 One-page memory map

```text
INTERFACE
────────────
interface g0/0
ip address IP MASK
no shutdown


STATIC ROUTING
────────────
ip route NETWORK MASK NEXT-HOP


RIP
────────────
router rip
version 2
no auto-summary
network NETWORK


EIGRP
────────────
router eigrp 100
network NETWORK


OSPF
────────────
router ospf 1
network NETWORK WILDCARD area 0


DHCP
────────────
ip dhcp excluded-address ...
ip dhcp pool LAN
network NETWORK MASK
default-router GATEWAY
dns-server 8.8.8.8


VLAN
────────────
vlan 10
name ICE

interface fa0/1
switchport mode access
switchport access vlan 10


TRUNK
────────────
interface g0/1
switchport mode trunk


VERIFY
────────────
show ip interface brief
show ip route
show running-config
show ip protocols
show ip eigrp neighbors
show ip ospf neighbor
show ip dhcp binding
show vlan brief
show interfaces trunk
ping IP
```

## ⭐ The 5 things I would memorize first

If you can only memorize five patterns before the exam:

**1. Interface**

```text
interface g0/0
ip address IP MASK
no shutdown
```

**2. RIP**

```text
router rip
version 2
no auto-summary
network NETWORK
```

**3. EIGRP**

```text
router eigrp 100
network NETWORK
```

**4. OSPF**

```text
router ospf 1
network NETWORK WILDCARD area 0
```

**5. Static**

```text
ip route NETWORK MASK NEXT-HOP
```

Then remember the verification command:

```text
show ip interface brief
show ip route
ping <IP>
```

That's enough to reconstruct most of the lab during the exam.
