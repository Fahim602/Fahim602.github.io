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
*Figure 2.2: Show's IP configuration of a PC within IT department*

![IT Ping](/assets/images/IT-ping.png)<br>
*Figure 2.3: Shows a successful ping between 2 devices within the IT subnet*

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

### Inter-VLAN Routing
