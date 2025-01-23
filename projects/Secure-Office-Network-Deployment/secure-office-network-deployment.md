---
layout: default
title: "Secure Office Network Deployment"
permalink: /projects/Secure-Office-Network-Deployment/secure-office-network-deployment
---

This project showcases the design, configuration, and security of a small office network using Cisco Packet Tracer. The goal was to create a functional and secure network that simulates real-world scenarios while demonstrating key networking and cybersecurity skills.

## Objectives:
- Network Planning & Setup
- VLAN Configuration
- Security Implementation
- Testing & Mitigation

## Skills Demonstrated:
- Networking fundamentals, VLANs, and IP addressing
- Security protocols like ACLs, Port Security, and SSH
- Troubleshooting and mitigation of network issues

## 1. Network Planning

**Objective: Design the initial layout of the network**

A network diagram was created to represent a small office network with three departments: management, HR and IT.

![Network Plan](/assets/images/network-plan.png)
*Figure 1: Network Diagram showing the planned layout of the office network*

As displayed in figure 1, the network has 3 switches connected to a router, each switch is assigned to a department and has 2 PC's and a server per switch. The departments each have their own VLAN, subnets assigned as follows:
IT: 192.168.1.0/24
Management: 192.168.2.0/24
HR: 192.168.3.0/24

## 2. Building the Network

**Objective: Create the physical and logical setup of the network in Packet Tracer**

This section covers the physical setup of the network in packet tracer, the logical configuration of devices and VLAN's and testing intra/inter-VLAN communication.

### 2.1 Physical Setup

**Objective: Setup the physical network in packet tracer and connect devices**

The physical network was created in packet tracer according to the plan, devices were connected using copper straight-through cables.

![Network Design](/assets/images/network-design.png)
*Figure 2.1: The network built in packet tracer*

### 2.2 Configuring Basic Networking

Objective: **Configure basic networking to allow intra-subnet communication**

The devices within each department were assigned static IP's and communication was successful:
IT: 192.168.1.x  Gateway: 192.168.1.1
Management: 192.168.2.x  Gateway: 192.168.2.1
HR: 192.168.3.x  Gateway: 192.168.3.1

![IP Config](/assets/images/IP-config)
*Figure 2.2: Show's IP configuration of a PC within IT department*

![IT Ping](/assets/images/IT-ping.png)
*Figure 2.3: Shows a successful ping between 2 devices within the IT subnet*

### 2.3 VLAN Configuration

**Objective: Segment the network into three VLANs for IT, Management and HR to improve traffic isolation**

Configuration Details:
VLAN's were configured on their respective switches:
- IT (VLAN 10) Ports FastEthernet0/1, FastEthernet 1/1, FastEthernet2/1
- Management (VLAN 20) Ports FastEthernet0/1, FastEthernet 1/1, FastEthernet2/1
- HR (VLAN 30) Ports FastEthernet0/1, FastEthernet 1/1, FastEthernet2/1
Trunk Ports: FastEthernet3/1 on each router, configured to carry traffic for all VLAN's to the router

VLAN Creation:

```bash
# Code for the creation of the IT VLAN 10
Switch enable
Switch# configure terminal
Switch(config)# vlan 10
Switch(config-vlan)# name IT
Switch(config-vlan)# exit

```
Assign ports to the VLAN:

```bash
# Port assignment for VLAN 10
Switch(config)# interface range fastethernet0/1, fastethernet1/1, fastethernet2/1
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 10
Switch(config-if-range)# exit

```

Configure the Trunk port:

```bash
# Trunk port config for all VLAN's
Switch(config)# interface fastethernet3/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# exit

```

Below are the outputs of the commands `show vlan brief`(*Figure 2.4*) and `show interfaces trunk`(*Figure 2.5*) which verify the creation of the VLAN and the assignment of the trunk port respectively:

![VLAN Brief](/assets/images/VLAN-brief)
*Figure 2.4*

![Trunk](/assets/images/trunk)
*Figure 2.5*

