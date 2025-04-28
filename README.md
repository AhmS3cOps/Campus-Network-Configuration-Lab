# **Campus Network Configuration Lab**

## 💑 **Table of Contents**
- [Description](#description)
- [Tools and Technologies Used](#tools-and-technologies-used)
- [Environment](#environment)
- [Task Instructions](#task-instructions)
- [My Configuration Steps](#my-configuration-steps)
  - [Part 1 - Basic Setup](#part-1---basic-setup)
  - [Part 2 - Advanced Switching](#part-2---advanced-switching)
  - [Part 3 - Redundancy, Routing and End Devices](#part-3---redundancy-routing-and-end-devices)
  - [Part 4 - DHCP, NAT, and Internet Access](#part-4---dhcp-nat-and-internet-access)
- [Verification Screenshots](#verification-screenshots)
- [Conclusion](#conclusion)

---

## **Description**
This project covers the full setup of a campus network in Cisco Packet Tracer: VLANs, trunking, spanning-tree optimization, redundancy with HSRP, routing with EIGRP, DHCP services, NAT/PAT configuration, and Internet access verification.

---

## **Tools and Technologies Used**
- **Cisco Packet Tracer**
- **Cisco CLI (Command Line Interface)**
- **Spanning Tree Protocol (STP)**
- **HSRP (Hot Standby Router Protocol)**
- **EIGRP (Enhanced Interior Gateway Routing Protocol)**
- **DHCP, NAT, PAT**

---

## **Environment**
- **Operating System**: Windows 11
- **Simulator**: Cisco Packet Tracer 8.x

---
## **Initial Topology**

<p align="center">
  <img src="https://imgur.com/gKUQOfm.png" width="70%" alt="Ping to Cisco.com Success"/>
</p>

---
## **Task Instructions**

### **Part 1**
Configure the network:
1. Configure Host Names
2. Shutdown unused interfaces
3. Configure `enable` password and `vty` passwords to "cisco"
4. Set VTP mode to Transparent and domain name to `ccna`
5. Add VLANs 10, 20, 30, 100
6. Configure ports between switches as trunks
7. Configure access ports on links to PCs

---

### **Part 2**
Configure the network:
1. Use CDP and LLDP to verify links
2. Verify which ports are forwarding/blocking (spanning tree)
3. Optimize spanning tree: Core1 is root for odd VLANs, Core2 for even VLANs
4. Configure EtherChannel on core switches
5. Enable interVLAN Layer 3 routing on Core switches
6. Assign management IP addresses to access layer switches in VLAN 1

---

### **Part 3**
Configure the network:
1. Configure and optimize HSRP
2. Configure EIGRP on Core switches and ISR router
3. Configure PCs and Server
4. Verify that PCs can ping each other and the Server
5. Verify connectivity between switches, server, and PCs
6. Router IPs:
   - g0/0/0 = 10.1.1.253/24
   - g0/0/1 = DHCP

---

### **Part 4**
Configure the network:
1. Configure DHCP on Router g0/0/1
2. Enable NAT (PAT) to translate the internal network
3. Verify PCs can ping cisco.com

---

## **My Configuration Steps**

### **Part 1 - Basic Setup**

> 🔹 *Set hostnames and shutdown unused interfaces.*
```plaintext
enable
config t
hostname <hostname>

interface range g1/0/4 - 19
shutdown
interface range g1/0/21 - 22
shutdown
interface range g1/1/1 - 4
shutdown
```

> 🔹 *Set console and VTY passwords to "cisco".*
```plaintext
config t
enable password cisco
line vty 0 4
password cisco
login
exit
```

> 🔹 *Configure VTP Transparent mode with domain "ccna".*
```plaintext
vtp mode transparent
vtp domain ccna
```

> 🔹 *Add VLANs 10, 20, 30, 100.*
```plaintext
vlan 10
vlan 20
vlan 30
vlan 100
```

> 🔹 *Configure trunk links between switches.*
```plaintext
interface range g1/0/1 - 3
switchport mode trunk
no shutdown
```

> 🔹 *Configure access ports for PCs and Server.*
```plaintext
interface g1/0/20
switchport mode access
switchport access vlan 100
no shutdown

interface g1/0/1
switchport mode access
switchport access vlan 10 (or 20, 30 for PC2, PC3)
no shutdown
```

---

### **Part 2 - Advanced Switching**

> 🔹 *Verify links using CDP and LLDP.*
```plaintext
show cdp neighbors
show lldp neighbors
```

> 🔹 *Verify spanning tree status.*
```plaintext
show spanning-tree
```

> 🔹 *Optimize spanning tree priorities.*
```plaintext
Core1:
spanning-tree vlan 1,10 priority 0
spanning-tree vlan 20,30,100 priority 4096

Core2:
spanning-tree vlan 20,30,100 priority 0
spanning-tree vlan 1,10 priority 4096
```

> 🔹 *Configure EtherChannel between Core switches.*
```plaintext
interface range g1/0/23 - 24
shutdown
channel-group 1 mode active
switchport mode trunk
no shutdown

interface port-channel 1
switchport mode trunk
```

> 🔹 *Enable interVLAN routing.*
```plaintext
ip routing
interface vlan <id>
ip address <IP> <Subnet Mask>
no shutdown
```

> 🔹 *Assign management IP addresses to access switches.*
```plaintext
interface vlan 1
ip address <Management IP> <Subnet>
no shutdown
```

---

### **Part 3 - Redundancy, Routing and End Devices**

> 🔹 *Configure HSRP on VLANs.*
```plaintext
interface vlan <vlan-id>
standby 1 priority 200
standby 1 preempt
standby 1 ip <Virtual IP>
```

> 🔹 *Configure EIGRP.*
```plaintext
router eigrp <AS-number>
network <Network> <Wildcard>
no auto-summary
```

> 🔹 *Configure PCs and Server static IPs and gateways.*

✅ **Ping between PCs and server successful.**

---

### **Part 4 - DHCP, NAT, and Internet Access**

> 🔹 *Configure DHCP client on Router g0/0/1.*
```plaintext
interface g0/0/1
ip address dhcp
```

> 🔹 *Enable NAT (PAT) and ACL.*
```plaintext
access-list 1 permit 10.1.0.0 0.0.255.255
ip nat inside source list 1 interface g0/0/1 overload
```

> 🔹 *Redistribute static route into EIGRP.*
```plaintext
router eigrp 10
redistribute static metric 10000 1000 255 1 1500
```

> 🔹 *Set DNS server.*
```plaintext
ip domain-lookup
ip name-server 8.8.8.8
```

✅ **Ping to cisco.com successful from PCs.**

---

## **Verification Screenshots**

### **Ping Tests Between PCs and Server**
<p align="center">
  <img src="https://imgur.com/mrxU7ml.png" width="70%" alt="Ping Between PCs and Server Success"/>
  <img src="https://imgur.com/ccK9wHm.png" width="70%" alt="Ping Between PCs and Server Success"/>
  <img src="https://imgur.com/SgsUevp.png" width="70%" alt="Ping Between PCs and Server Success"/>
</p>

---

### **Ping Test to Internet (cisco.com)**
<p align="center">
  <img src="https://imgur.com/t9CrLDV.png" width="70%" alt="Ping to Cisco.com Success"/>
</p>

---

## ✅ **Conclusion**
This lab successfully demonstrates:
- Full VLAN, trunking, and spanning-tree configuration
- Redundancy using HSRP
- Routing with EIGRP
- DHCP and NAT for Internet access
- End-to-end device communication verified

---


