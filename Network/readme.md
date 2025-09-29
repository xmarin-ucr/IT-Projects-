## Data Communication Networks – University Coursework

This repository contains practical assignments and documentation developed for the Data Communication Networks course at the University of Costa Rica.

The work focuses on network design, simulation, and protocol analysis using Cisco Packet Tracer. Each submission demonstrates a solid understanding of networking principles and includes well-structured documentation to support reproducibility and clarity.

## Tools Used
* Cisco Packet Tracer: Network simulation and topology design

* Basic CLI Commands: For router/switch configuration and troubleshooting

* Documentation: Clear and organized technical reporting

### Labs

- **Lab 1: Introduction to Packet Tracer**  
  Basic topology creation, device configuration, and connectivity testing.

- **Lab 2: Basic Network Setup**  
  Static routing between buildings, subnet planning, and connectivity validation.


## Project Stages

### Stage 1 - ECCI Network Design

- Design of a segmented network across two buildings, including:

- VLAN-based lab separation (VLANs 101–104)

- Wireless access via VLAN 201

- Layer 3 switching and trunking

- Centralized services (DHCP, DNS, NAT, Firewall)

- IP addressing using 10.1.X.X/16 with dynamic allocation

- NAT configuration for internet access using public IP pools

### Stage 2 – UCR Backbone and ECCI Firewall

- Expansion of the network to include:

- Four Point of Presence (POP) routers with OSPF routing

- Subnet planning and NAT pools per POP

- Public and private IP integration

- Firewall configuration with:

1. Port 80 access for web services

2. SSH access restricted to one internal admin host

3. ICMP traffic blocked

### Stage 3 – BGP and DNS Implementation (20 pts)

- Final integration of external routing and name resolution:

- BGP configuration to advertise UCR’s public network (163.178.104.0)

- DNS hierarchy setup for domains:

1. ecci.ucr.ac.cr

2. mediacionvirtual.ucr.ac.cr

3. ucr.ac.cr

- Routing isolation between providers

- Functional testing of domain resolution and connectivity

##  Deliverables Format

Each stage includes:

* Configuration dumps for routers and switches

* A written report explaining key commands and design decisions

* Screenshots demonstrating:

1. Correct IP addressing

2. Successful and failed connectivity tests

## Note
All materials are in Spanish, as they were originally submitted for university coursework.
