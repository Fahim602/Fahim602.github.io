---
layout: default
title: "Secure Office Network Deployment"
permalink: /projects/Secure-Office-Network-Deployment/secure-office-network-deployment
---

This project showcases the design, configuration, and security of a small office network using Cisco Packet Tracer. The goal was to create a functional and secure network that simulates real-world scenarios while demonstrating key networking and cybersecurity skills.

## Table of Contents
- [1. Network Planning](#1-network-planning)
- [2. Building the Network](#2-building-the-network)
  - [2.1 Physical Setup](#21-physical-setup)
  - [2.2 Configuring Basic Networking](#22-configuring-basic-networking)
- [3. VLAN Configuration and Inter-VLAN Routing](#3-vlan-configuration-and-inter-vlan-routing)
  - [3.1 VLAN Configuration](#31-vlan-configuration)
  - [3.2 Inter-VLAN Routing](#32-inter-vlan-routing)
- [4. DHCP Configuration](#4-dhcp-configuration)
- [5. NAT Configuration](#5-nat-configuration)
  - [5.1 Configuring NAT](#51-configuring-nat)
  - [5.2 Configuring Test Endpoint](#52-configuring-test-endpoint)
  - [5.3 Configuring Routing](#53-configuring-routing)
  - [5.4 Testing](#54-testing)

## Objectives:
- Network Planning & Setup
- VLAN Configuration
- Security Implementation
- Testing & Mitigation

## Skills Demonstrated:
- Networking fundamentals, VLANs, and IP addressing
- Security protocols like ACLs, Port Security, and SSH
- Troubleshooting and mitigation of network issues  
<br><br>

---  

<br>

## 1. Network Planning

**Objective: Design the initial layout of the network**

A network diagram was created to represent a small office network with three departments: management, HR and IT.

![Network Plan](/assets/images/network-plan.png)
*Figure 1: Network Diagram showing the planned layout of the office network*

As displayed in figure 1, the network has 3 switches connected to a router, each switch is assigned to a department and has 2 PCs and a server per switch. The departments each have their own VLAN, subnets assigned as follows:  
IT: 192.168.1.0/24  
Management: 192.168.2.0/24  
HR: 192.168.3.0/24
<br><br>

---  

<br>


## 2. Building the Network

**Objective: Create the physical and logical setup of the network in Packet Tracer**

This section covers the physical setup of the network in packet tracer and verifying intra-subnet communication.

### 2.1 Physical Setup

**Objective: Setup the physical network in packet tracer and connect devices**

The physical network was created in packet tracer according to the plan, devices were connected using copper straight-through cables.

![Network Design](/assets/images/network-design.png)<br>
*Figure 2.1: The network built in packet tracer*

### 2.2 Configuring Basic Networking

Objective: **Configure basic networking to allow intra-subnet communication**

The devices within each department were assigned static IP's and communication was successful:  
IT: 192.168.1.x  Gateway: 192.168.1.1  
Management: 192.168.2.x  Gateway: 192.168.2.1  
HR: 192.168.3.x  Gateway: 192.168.3.1  

![IP Config](/assets/images/IP-config.png)<br>
*Figure 2.2: Shows IP configuration of a PC within IT department*

![IT Ping](/assets/images/IT-ping.png)<br>
*Figure 2.3: Shows a successful ping between 2 devices within the IT subnet*
<br><br>

---  

<br>


## 3. VLAN Configuration and Inter-VLAN Routing
 
**Objective: Segment traffic using VLANs and enable communication between VLANs**

### 3.1 VLAN Configuration

**Objective: Segment the network into three VLANs for IT, Management and HR to improve traffic isolation**

Configuration Details:  
VLANs were configured on their respective switches:
- IT (VLAN 10): Ports FastEthernet0/1, FastEthernet 1/1, FastEthernet2/1  
- Management (VLAN 20): Ports FastEthernet0/1, FastEthernet 1/1, FastEthernet2/1  
- HR (VLAN 30): Ports FastEthernet0/1, FastEthernet 1/1, FastEthernet2/1  

Trunk Ports: FastEthernet3/1 on each router, configured to carry traffic for all VLANs to the router  
Without trunk ports, devices in different VLANs wouldn't be able to communicate.

#### VLAN Creation:

```bash
# Code for the creation of the IT VLAN 10
Switch enable
Switch# configure terminal
Switch(config)# vlan 10             # Creates VLAN 10
Switch(config-vlan)# name IT        # Name VLAN 10 "IT"
Switch(config-vlan)# exit           # Exit VLAN config mode
```

#### Assign ports to the VLAN:

```bash
# Port assignment for VLAN 10
Switch(config)# interface range Fa0/1, Fa1/1, Fa2/1     # Enter interface config mode for the 3 ports
Switch(config-if-range)# switchport mode access         # Change the port mode to access
Switch(config-if-range)# switchport access vlan 10      # Assign the ports to VLAN 10  
Switch(config-if-range)# exit                           # Exit interface config
```

#### Configure the Trunk port:

```bash
# Trunk port config for all VLANs
Switch(config)# interface Fa3/1             # Enter configuration interface for port Fa3/1
Switch(config-if)# switchport mode trunk    # Configure Fa3/1 as a trunk
Switch(config-if)# exit                     # Exit interface config mode
```  

> This configuration sets FastEthernet3/1 as a trunk port to carry traffic for all VLANs ensuring interswitch routing and connectivity to the router.


Below are the outputs of the commands `show vlan brief` (*Figure 3.1*) and `show interfaces trunk` (*Figure 3.2*) which verify the creation of the VLAN and the assignment of the trunk port respectively:

![VLAN Brief](/assets/images/VLAN-brief.png)<br>
*Figure 3.1: Verification of VLAN configuration using `show vlan brief`*

![Trunk](/assets/images/trunk.png)<br>
*Figure 3.2: Verification of Trunk configuration using `show interfaces trunk`*

Now that the VLANs have been successfully created and configured, the next step is to configure inter-vlan routing to enable communication between the departments.

### 3.2 Inter-VLAN Routing

**Objective: Enable inter-VLAN communication through the configuration of a router**

Router-on-a-stick will be used to enable communication between the VLANs. RoaS is an inter-VLAN routing method where a single router interface is divided into multiple logical interfaces (subinterfaces), each representing a VLAN. The router is connected to the switch via a single cable which allows it to logically communicate with all VLANs using 802.1q tagging. This tagging applies a unique identifier to each packet, ensuring it is associated with the correct VLAN.

> **Troubleshooting: Router Subinterface Issue**  
Whilst configuring RoaS it was discovered that the 819HGW router doesn't support sub-interfaces, a critical feature for handling multiple VLANs on a single physical connection. As a result it was replaced with the Cisco 1841 router.

> **Troubleshooting: Port Compatibility Issue**  
During the setup of inter-switch trunk links, it was discovered that the Fa3/1 ports used as trunks on the HR and IT switches don't support fibre optic cables, whilst the Fa4/1 and Fa5/1 ports on the management switch only support fibre optic. As a result, the trunk ports were reassigned to improve compatibility.

```bash
# An example of the trunk reassigment on the IT switch
IT-Switch(config)# interface fastethernet3/1
IT-Switch(config-if)# no switchport mode trunk  # Remove trunk from Fa3/1

IT-Switch(config)# interface fastethernet4/1
IT-Switch(config-if)# switchport mode trunk     # Assign Fa4/1 as a trunk
IT-Switch(config-if)# no shutdown
```

#### Creating Subinterfaces for VLANs:

For RoaS to enable inter-VLAN communication, it is necessary to create subinterfaces on the router for each VLAN. Each subinterface acts as the gateway for its respective VLAN and uses 802.1q encapsulation to tag traffic with the appropriate VLAN id.

```bash
# Example for VLAN 10 (IT)
Router> enable
Router# configure terminal
Router(config)# interface fastethernet0/0.10                    # Create a subinterface for VLAN 10 on the router's Fa0/0 interface
Router(config-subif)# encapsulation dot1Q 10                    # Enable 802.1q tagging for VLAN 10
Router(config-subif)# ip address 192.168.1.1 255.255.255.0      # Assign the default gateway ip address for VLAN 10

# Process repeated for VLAN 20 (Management) and VLAN 30 (HR)
```
> Note: 802.1q tagging is essential for RoaS as it ensures that packets are routed correctly between VLANs

<table>
  <thead>
    <tr>
      <th>Subinterface</th>
      <th>VLAN ID</th>
      <th>IP Address</th>
      <th>Purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Fa0/0.10</td>
      <td>10</td>
      <td>192.168.1.1/24</td>
      <td>IT Department</td>
    </tr>
    <tr>
      <td>Fa0/0.20</td>
      <td>20</td>
      <td>192.168.2.1/24</td>
      <td>Management Department</td>
    </tr>
    <tr>
      <td>Fa0/0.30</td>
      <td>30</td>
      <td>192.168.3.1/24</td>
      <td>HR Department</td>
    </tr>
  </tbody>
</table>

![Subinterfaces](/assets/images/subinterfaces.png)<br>
*Figure 3.3: Successful creation of subinterfaces*


![Routing Table](/assets/images/IP-route.png)<br>
*Figure 3.4: Routing table using command `show ip route`*

> The router has now been successfully configured with subinterfaces for VLANs 10, 20 and 30. Each subinterface is tagged with its own VLAN and assigned a default gateway IP address. The routing table shows that subnets 192.168.1.0/24, 192.168.2.0/24 and 192.168.3.0/24 are directly connected to subinterfaces Fa0/0.10, Fa0/0.20 and Fa0/0.30 respectively, this ensures that inter-VLAN communication is possible.

#### Reconfigure Trunk Ports due to Compatibility Issues

Trunk ports were configured previously in [Configure the Trunk Port](#configure-the-trunk-port), however these must be reconfigured due to compatibility issues.

```bash
# Process to reassign trunks on IT and HR switches
Switch# configure terminal
Switch(config) interface fastethernet3/1
Switch(config-if) no switchport mode trunk    # Disable Fa3/1 as trunk
Switch(config-if) exit
# Fa4/1 and Fa5/1 then assigned as trunks
```

```bash
# Assigning trunk ports on Management switch
Switch(config)# interface fastethernet4/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20,30
Switch(config-if)# no shutdown

Switch(config)# interface fastethernet5/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20,30
Switch(config-if)# no shutdown
```
> Note: Management switch now has 3 active trunk ports, Fa3/1, Fa4/1 and Fa5/1. The other departments have 2, Fa4/1 and Fa5/1

![Trunk Ports](/assets/images/trunks.png)<br>
*Figure 3.5: Trunk ports configured on management switch and VLANs allowed access*

Trunk ports have now been successfully configured on the Management switch for VLANs 10, 20 and 30, thus ensuring proper VLAN tagging and traffic flow between the router and switches.

#### Inter-VLAN Communication Testing

Inter-VLAN communication was tested by pinging devices in different VLANs.

![IT to Management Ping](/assets/images/VLAN-ping2.png)<br>
*Figure 3.6: A ping from IT PC-1 (192.168.1.10) in VLAN 10 to Management PC-1 (192.168.2.10) VLAN 20*

![HR to IT ping](/assets/images/VLAN-ping1.png)<br>
*Figure 3.7: A ping from HR PC-1 (192.168.3.10) in VLAN 30 to IT PC-1 (192.168.1.10) VLAN 10*

As shown above, inter-VLAN communication and routing is working as intended, the router has successfully forwarded traffic between VLANs using the subinterfaces.

![Updated Network](/assets/images/updated-network.png)
*Figure 3.8: An annotated image of the network build at this stage*

Figure 3.8 displays the updated network topology with the RoaS connection, the trunk ports are labelled along with the information about each department.
<br><br>

---  

<br>


## 4. DHCP Configuration

**Objective: Automate IP address assignment across the network for devices in VLANs, IT, Management and HR**

Dynamic Host Configuration Protocol (DHCP) is a protocol used to automatically assign IP addresses and other network configuration parameters (e.g. subnet mask, gateway). As a result of this, DHCP is an incredibly useful tool in networking, it has several key benefits:
1. Automation:
- DHCP eliminates the need to manually assign IPs, increasing efficiency and reducing errors.
2. Flexibility and Scalability:
- Devices can easily join the network and obtain an IP, very useful for large scale networks.
3. Dynamic Updates:
- Reclaims unused IPs when devices leave the network, ensuring efficient utilisation of IPs.
4. Centralised Management:
- IP assignments can be controlled from a single DHCP server

In this project, DHCP was configured on the router to automatically assign IP addresses to devices in each of the VLANs. While this isn't strictly necessary for a network of this size, I decided to implement it, for the sake of scalability, adding a touch of complexity and getting practical experience with dynamic IP address allocation.

**DHCP Configuration Plan:**

<table>
  <thead>
    <tr>
      <th>VLAN</th>
      <th>Subnet</th>
      <th>Excluded IPs</th>
      <th>DHCP Pool Range</th>
      <th>Gateway</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>IT (VLAN 10)</td>
      <td>192.168.1.0/24</td>
      <td>192.168.1.1 – 192.168.1.11</td>
      <td>192.168.1.12 – 192.168.1.254</td>
      <td>192.168.1.1</td>
    </tr>
    <tr>
      <td>Management (VLAN 20)</td>
      <td>192.168.2.0/24</td>
      <td>192.168.2.1 – 192.168.2.11</td>
      <td>192.168.2.12 – 192.168.2.254</td>
      <td>192.168.2.1</td>
    </tr>
    <tr>
      <td>HR (VLAN 30)</td>
      <td>192.168.3.0/24</td>
      <td>192.168.3.1 – 192.168.3.11</td>
      <td>192.168.3.12 – 192.168.3.254</td>
      <td>192.168.3.1</td>
    </tr>
  </tbody>
</table>
<p><strong>Note:</strong> Excluded IP ranges are reserved for static assignments to critical devices like servers and routers.</p>
<br><br>

---

<br>

### DHCP Configuration Steps

#### Excluding Static IPs

```bash
Router(config)# ip dhcp excluded-address 192.168.1.1 192.168.1.11    # Specifies the address range to exclude
Router(config)# ip dhcp excluded-address 192.168.2.1 192.168.2.11
Router(config)# ip dhcp excluded-address 192.168.3.1 192.168.3.11
```

This ensures that static IP addresses are not automatically assigned by DHCP.

![Excluded IP range](/assets/images/excluded-dhcp.png)<br>
*Figure 4.1: Verifies the excluded static IP address range*

#### Create DHCP pools for each VLAN

```bash
Router(config)# ip dhcp pool VLAN10                     
Router(dhcp-config)# network 192.168.1.0 255.255.255.0      # Define the subnet
Router(dhcp-config)# default-router 192.168.1.1             # Gateway for this VLAN
Router(dhcp-config)# dns-server 8.8.8.8                     # Specify DNS server
Router(dhcp-config)# exit

# Process repeated for vlan 20 and 30
```

> Note: DNS server specification is optional here but will be relevant when working with NAT. 8.8.8.8 is the Google Public DNS.

#### DHCP Testing

To test the DHCP IP allocation, two new PC's were added to the network, one to the IT department(VLAN 10), and the other to HR (VLAN 30). The PC's were set to DHCP IP allocation, shown below are the results.

![IT-PC DHCP](assets/images/it-pc-dhcp.png)<br>
*Figure 4.2: Clearly displays DHCP allocation from the PC configuration page, IP outside of excluded range*

The systems were able to ping their respective gateway addresses and also were capable of intra/inter VLAN communication.  
![DHCP Ping](assets/images/it-dhcp-ping.png)<br>
*Figure 4.3: Ping from PC in IT VLAN with auto assigned DHCP IP to it's gateway, confirming network connectivity*

By using the command `show ip dhcp binding` we are also able to see the devices that have been dynamically allocated IPs.   
![DHCP Binding](assets/images/dhcp-binding.png)<br>
*Figure 4.4: Output of `show ip dhcp binding` which displays the IPs allocated and hardware addresses of the devices*

> The DHCP server was successfully configured to dynamically allocate IP addresses whilst ignoring IPs within the excluded range. Devices were tested for connectivity and passed.
<br><br>

---

<br>

## 5. NAT Configuration

**Objective: Enable devices within internal network to access external networks by configuring NAT on the router**

Network Address Translation (NAT) is used to modify packets as they pass through a router. It's a method to map multiple private IP addresses within a network onto a public IP address, enabling communication with external networks whilst maintaining the security of the internal network.
There are several types of NAT:
1. Static NAT - One private IP is mapped to one public IP (1-1 Translation)
2. Dynamic NAT - A pool of public IP's is assigned dynamically to private IPs
3. Dynamic NAT with PAT - Multiple devices share one public IP by using different ports

For this project I will be using Dynamic NAT with PAT as it provides scalability and an efficient usage of ip addresses. For the purpose of testing NAT I will also be setting up an "ISP" router to simulate an external network connection and creating a "Google" server (8.8.8.8) to act as a test endpoint.

<table>
  <thead>
    <tr>
      <th>Component</th>
      <th>Configuration</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Internal Networks</td>
      <td>192.168.1.0/24 (IT), 192.168.2.0/24 (Management), 192.168.3.0/24 (HR)</td>
    </tr>
    <tr>
      <td>Public IP</td>
      <td>203.0.113.1 (Router's Fa0/1 interface)</td>
    </tr>
    <tr>
      <td>NAT Type</td>
      <td>Dynamic NAT with PAT</td>
    </tr>
    <tr>
      <td>Access Control List (ACL)</td>
      <td>Permit traffic from internal networks (192.168.1.0, 192.168.2.0, 192.168.3.0)</td>
    </tr>
  </tbody>
</table>

> Internal VLANs (IT, Management and HR) will share one public IP (203.0.113.1)  
PAT assigns unique port numbers to track sessions


### 5.1 Configuring NAT

**Objective: Configure Dynamic NAT with PAT to allow communication between devices in internal VLANs and external networks**

#### Define NAT Outside and Inside Interfaces

To enable NAT, the internal VLAN subinterfaces were defined as inside interfaces, while the public facing Fa0/1 interface (connected to the ISP) was defined as outside.

```bash
Router(config)# interface fastethernet0/0
Router(config-if)# ip nat inside                # Defines Fa0/0 as inside (internal network)

Router(config)# interface fastethernet0/0.10
Router(config-subif)# ip nat inside             # IT VLAN

Router(config)# interface fastethernet0/0.20
Router(config-subif)# ip nat inside             # Management VLAN

Router(config)# interface fastethernet0/0.30
Router(config-subif)# ip nat inside             # HR VLAN

Router(config)# interface fastethernet0/1
Router(config-if)# ip nat outside               # Defines Fa0/1 as outside (external network)
```


![NAT-interfaces](/assets/images/NAT-stats.png) <br>
*Figure 5.1: Output displays that inside and outside interfaces have been correctly assigned*

#### Create an ACL to Permit Internal Traffic

An access control list was created to specify which internal subnets are allowed to be translated using NAT.

```bash
Router(config)# access-list 1 permit 192.168.1.0 0.0.0.255    # IT VLAN
Router(config)# access-list 1 permit 192.168.2.0 0.0.0.255    # Management VLAN
Router(config)# access-list 1 permit 192.168.3.0 0.0.0.255    # HR VLAN
```

> The ACL configured above permits NAT only for internal subnets, preventing unauthorised traffic from being translated

#### Configure NAT Overload (PAT)

Links ACL to the public facing interface and enable NAT overload

```bash
Router(config)# ip nat inside source list 1 interface fastethernet0/1 overload
```
This command will translate traffic from all devices in the VLANs to the public facing IP assigned to Fa0/1 (203.0.113.1)

### 5.2 Configuring Test Endpoint

**Objective: Setup an ISP router and Google server to simulate an external connection for the purpose of testing NAT**

**Configuring ISP router:**

```bash
ISP-Router(config)# interface fastethernet0/0                  # Interface connected to network router
ISP-Router(config-if)# ip address 203.0.113.2 255.255.255.252  # Public IP facing the router
ISP-Router(config-if)# no shutdown

ISP-Router(config)# interface fastethernet0/1                  # Interface connected to "Google" server 
ISP-Router(config-if)# ip address 8.8.8.1 255.255.255.0        # Acts as the "Google" gateway
ISP-Router(config-if)# no shutdown

ISP-Router(config)# ip route 0.0.0.0 0.0.0.0 203.0.113.1       # Default route to the internal router
```

**Configuring Google server:**

```bash
Google-Server(config)# ip address 8.8.8.8 255.255.255.0
Google-Server(config)# gateway 8.8.8.1
```

### 5.3 Configuring Routing

**Objective: Configure default and return routes between main router and ISP**

For NAT to work effectively, the router must know where to forward external traffic. There must be a default route, which forwards unknown traffic to the ISP router, and a return route, which sends replies back to the main router. Static routing was chosen over dynamic routing for this project as this is a network with a simple, fixed structure. For a larger network a dynamic routing protocol like OSPF would be preferable to allow automation.

```bash
Router(config)# ip route 0.0.0.0 0.0.0.0 203.0.113.2
```
*Configures a static route on the main router*

```bash
ISP-Router(config)# ip route 192.168.1.0 255.255.255.0 203.0.113.1
ISP-Router(config)# ip route 192.168.2.0 255.255.255.0 203.0.113.1
ISP-Router(config)# ip route 192.168.3.0 255.255.255.0 203.0.113.1
```
*Configures several return routes corresponding to the subnets on the ISP router*

> NAT has now been configured correctly and routing should be working effectively

### 5.4 Testing



To verify NAT is working correctly, a ping must be made from one of the internal network devices to the external server (8.8.8.8). From there the IP translations can be verified.

![No-NAT ping](assets/images/noNAT-ping.png)<br>
*Figure 5.2: A ping between a PC and the 8.8.8.8 server before NAT was successfully configured*

![NAT Ping](assets/images/IT-NAT-ping.png)<br>
*Figure 5.3: A ping from a PC (192.168.1.10) in the IT department to the 8.8.8.8 server after NAT has been configured*

As shown above, once NAT and routing were successfully configured, the devices were able to ping the google server.

We can verify that NAT is translating IP's by using the `show IP nat translation` command. The results are shown below.

![NAT Translation](assets/images/NAT-translations.png)<br>
*Figure 5.4: Results of IP translation by NAT into public IP (203.0.113.1)*

We can see from the results that NAT successfully translated two different IP addresses - 192.168.1.10 and 192.168.1.12 - into a singular public facing IP (203.0.113.1) followed by a port number. This verifies that Dynamic NAT with PAT has been configured correctly and multiple devices can now connect externally while using the same global IP.
<br><br>

---

<br>

Here is the updated network topology after the addition of the newer components:  

![Updated Network](assets/images/NAT-network.png)
<br><br>

---

<br>


