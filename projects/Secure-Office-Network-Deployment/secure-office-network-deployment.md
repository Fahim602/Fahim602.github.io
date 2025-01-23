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

Objective: Design the initial layout of the network

A network diagram was created to represent a small office network with three departments: management, HR and IT.

<img src="/assets/images/network-plan.png" alt="Network Plan" width="60%" />
*Figure 1: Network Diagram showing the planned layout of the office network*

As displayed in figure 1, the network has 3 switches connected to a router, each switch is assigned to a department and has 2 PC's and a server per switch. The departments each have their own VLAN, subnets assigned as follows:
IT: 192.168.1.0/24
Management: 192.168.2.0/24
HR: 192.168.3.0/24

## 2. Building the Network

Objective: Create the physical and logical setup in Packet Tracer

