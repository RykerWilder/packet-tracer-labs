# Spanning Tree Protocol Configuration

## Project Description

The project simulates a LAN network composed of three switches (SW1, SW2, SW3) connected together in a redundant triangular topology, to which four client laptops are connected. The purpose of the exercise is to configure the Spanning Tree Protocol (STP) to prevent network loops caused by redundant links between the switches, while ensuring the availability of an alternate path in the event of a failure.

### Network Topology

- SW1 is designated as the network's Root Bridge and is connected to both SW2 and SW3 via ports Fa0/1 and Fa1/1.
- SW2 and SW3 are connected to each other via ports Fa2/1 and are both connected to SW1 via port Fa3/1, thus forming a loop between the three switches.
- **SW2** is connected to:
- **Laptop0** (port `Fa0/1`) — IP `192.168.1.10`
- **Laptop1** (port `Fa1/1`) — IP `192.168.1.11`
- **SW3** is connected to:
- **Laptop2** (port `Fa0/1`) — IP `192.168.1.12`
- **Laptop3** (port `Fa1/1`) — IP `192.168.1.13`

The connection between `SW2 Fa2/1` and `SW3 Fa2/1` represents the link that, in the absence of STP, would close the physical loop between the three switches, causing broadcast storms and network instability. STP intervenes by logically blocking one of the redundant ports to eliminate the loop, while still keeping the physical connection available as a backup.

---

## STP (Spanning Tree Protocol)

A network protocol that prevents network loops by creating a loop-free topology. It determines the shortest and most stable path between devices in a LAN by selecting a primary bridge (Root Bridge).

### Root Bridge Election

1. Each switch has a unique ID (priority + MAC Address).
2. The switches exchange BPDU (Bridge Protocol Data Unit) messages containing their own ID and the ID of the known root bridge.
3. Election occurs by comparing the IDs received: the bridge with the lowest ID value becomes the root.
4. The other switches calculate the routes to reach it based on the Path Cost, which depends on the link bandwidth.

---

## Switch Configuration

### SW1 - Root

```
spanning-tree mode rapid-pvst
spanning-tree vlan 1 root primary
int range fa0/1-24
switchport mode access
spanning-tree portfast
spanning-tree bpduguard enable
```

SW1 is configured as the **primary root bridge** for VLAN 1: the `root primary` command automatically lowers the switch's priority to the lowest possible value available in the network, ensuring it is elected as the root of the spanning tree. Ports to hosts are set to access mode, with **PortFast** (to skip listening/learning states and immediately switch to forwarding) and **BPDU Guard** (to automatically disable the port if it receives a BPDU, protecting against unauthorized topologies).

### SW2 - SW3

```
spanning-tree mode rapid-pvst
spanning-tree vlan 1 root secondary
switchport mode access
spanning-tree portfast
spanning-tree bpduguard enable
```

SW2 and SW3 are configured as secondary root bridges: if SW1 fails or shuts down, one of them automatically takes over as the new root bridge, ensuring service continuity. PortFast and BPDU Guard features are also applied to the laptop access ports on these switches.

---

## Functional Summary

| Switch | STP Role | Ports to Root | Ports to Host |
|--------|- ... Fa0/1 (Laptop0), Fa1/1 (Laptop1) |
| SW3 | Root Secondary | Fa3/1 → SW1 | Fa0/1 (Laptop2), Fa1/1 (Laptop3) |

The redundant link between `SW2 Fa2/1` and `SW3 Fa2/1` is managed by STP: one of the two ports involved is placed in **blocking** state, eliminating the logical loop while maintaining the active physical connection as a backup path in the event of a link failure to SW1.