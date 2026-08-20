# RIP Routing Configuration Across Three Cisco Routers (Router0, Router1, Router2)

This project (simulated with Cisco Packet Tracer) demonstrates the configuration of dynamic routing using **RIP (Routing Information Protocol)** on three routers connected in a triangular topology, each of them serving its own LAN.

The goal is to show how RIP allows the routers to automatically learn and exchange routes for each other's LANs, so that hosts on `192.168.1.0/24`, `192.168.2.0/24`, and `192.168.3.0/24` can reach one another without the need to manually configure static routes on every device.

---

## Network Topology

The network consists of 3 routers (`Router0`, `Router1`, `Router2`), interconnected via serial links forming a triangle, each with its own LAN (switch + 2 laptops).

| Router | LAN Interface | LAN Network | Connected Hosts | Serial Interfaces |
|--------|----------------|-------------|------------------|--------------------|
| Router0 | Fa0/0 – 192.168.1.1 | 192.168.1.0/24 | Laptop0 (192.168.1.2), Laptop1 (192.168.1.3) | Se2/0 – 10.0.0.1, Se3/0 – 12.0.0.1 |
| Router1 | Fa0/0 – 192.168.2.1 | 192.168.2.0/24 | Laptop4 (192.168.2.2), Laptop5 (192.168.2.3) | Se2/0 – 10.0.0.2, Se3/0 – 11.0.0.1 |
| Router2 | Fa0/0 – 192.168.3.1 | 192.168.3.0/24 | Laptop2 (192.168.3.2), Laptop3 (192.168.3.3) | Se2/0 – 11.0.0.2, Se3/0 – 12.0.0.2 |

The three serial links between the routers form the WAN backbone of the topology:

- Router0 ↔ Router1 → network `10.0.0.0/8` (Router0 Se2/0, Router1 Se2/0)
- Router1 ↔ Router2 → network `11.0.0.0/8` (Router1 Se3/0, Router2 Se2/0)
- Router0 ↔ Router2 → network `12.0.0.0/8` (Router0 Se3/0, Router2 Se3/0)

Each router is directly connected only to its two neighbors and to its own LAN: it is RIP's job to propagate reachability information so that, for example, Laptop0 (on Router0's LAN) can reach Laptop2 (on Router2's LAN) even though the two routers are not directly linked to each other's LAN.

---

## CLI Commands Used (on `Router0`)

Below is the complete sequence of commands, along with an explanation of each. The same logic is then replicated on `Router1` and `Router2`, adapting the `network` statements to each router's own connected subnets.

### 1. Accessing privileged mode and global configuration mode

```
Router>enable
Router#configure terminal
```

- `enable` → moves from user mode (`>`) to privileged mode (`#`), required to view/modify the configuration.
- `configure terminal` (short form: `conf t`) → enters global configuration mode, from which router parameters (hostname, interfaces, routing protocols, etc.) can be changed.

### 2. Setting the hostname

```
Router(config)#hostname Router0
```

- Changes the router name from `Router` to `Router0`, useful for easily identifying the device in a network with multiple routers.

### 3. Configuring the interfaces

Before RIP can advertise a network, the corresponding interface must be up with the correct IP address.

```
Router0(config)#interface FastEthernet0/0
Router0(config-if)#ip address 192.168.1.1 255.255.255.0
Router0(config-if)#no shutdown
Router0(config-if)#exit

Router0(config)#interface Serial2/0
Router0(config-if)#ip address 10.0.0.1 255.0.0.0
Router0(config-if)#no shutdown
Router0(config-if)#exit

Router0(config)#interface Serial3/0
Router0(config-if)#ip address 12.0.0.1 255.0.0.0
Router0(config-if)#clock rate 64000
Router0(config-if)#no shutdown
Router0(config-if)#exit
```

- `ip address` → assigns the IP address and subnet mask to the interface.
- `no shutdown` → administratively enables the interface (interfaces are shut down by default).
- `clock rate 64000` → required only on the DCE end of a serial link (in this topology, Router0's Se3/0 side towards Router2), since one of the two ends must provide clocking for the connection to come up.

### 4. Enabling RIP and advertising the directly connected networks

```
Router0(config)#router rip
Router0(config-router)#version 2
Router0(config-router)#network 192.168.1.0
Router0(config-router)#network 10.0.0.0
Router0(config-router)#network 12.0.0.0
Router0(config-router)#no auto-summary
```

- `router rip` → enables the RIP routing process and enters router configuration sub-mode.
- `version 2` → forces RIP to use version 2 instead of the default version 1. RIPv2 supports subnet masks (classless routing) and multicast updates (224.0.0.9), making it the correct choice whenever the network uses subnetting or discontiguous networks, as in this topology.
- `network X.X.X.X` → tells RIP which directly connected networks to advertise and on which interfaces to run the protocol. Only the classful/major network address needs to be entered; RIP identifies the matching local interfaces automatically.
- `no auto-summary` → disables automatic summarization at classful network boundaries. Without this command, RIPv2 would still summarize routes by default when crossing network boundaries, which can cause reachability issues in networks with discontiguous or unevenly subnetted addressing. This is standard best practice when running RIPv2.

The same block of commands is applied on `Router1` and `Router2`, each one advertising its own LAN and its own two serial networks.

### 5. Verifying the routing table

```
Router0#show ip route
```

This displays the routes known to `Router0`, distinguishing directly connected networks (`C`) from those learned dynamically via RIP (`R`). After convergence, each router should show routes to the two LANs it is not directly connected to, learned through RIP.

```
Router0#show ip protocols
```

This confirms that RIP is running, which version is in use, which networks are being advertised, and which neighboring routers are being heard from — useful as supporting evidence for the project report.

---

## Final Configuration Summary on `Router0`

```
hostname Router0
!
interface FastEthernet0/0
 ip address 192.168.1.1 255.255.255.0
 no shutdown
!
interface Serial2/0
 ip address 10.0.0.1 255.0.0.0
 no shutdown
!
interface Serial3/0
 ip address 12.0.0.1 255.0.0.0
 clock rate 64000
 no shutdown
!
router rip
 version 2
 network 192.168.1.0
 network 10.0.0.0
 network 12.0.0.0
 no auto-summary
```

## Final Configuration Summary on `Router1`

```
hostname Router1
!
interface FastEthernet0/0
 ip address 192.168.2.1 255.255.255.0
 no shutdown
!
interface Serial2/0
 ip address 10.0.0.2 255.0.0.0
 no shutdown
!
interface Serial3/0
 ip address 11.0.0.1 255.0.0.0
 clock rate 64000
 no shutdown
!
router rip
 version 2
 network 192.168.2.0
 network 10.0.0.0
 network 11.0.0.0
 no auto-summary
```

## Final Configuration Summary on `Router2`

```
hostname Router2
!
interface FastEthernet0/0
 ip address 192.168.3.1 255.255.255.0
 no shutdown
!
interface Serial2/0
 ip address 11.0.0.2 255.0.0.0
 no shutdown
!
interface Serial3/0
 ip address 12.0.0.2 255.0.0.0
 no shutdown
!
router rip
 version 2
 network 192.168.3.0
 network 11.0.0.0
 network 12.0.0.0
 no auto-summary
```

The `router rip` block must be configured on all three routers, each one declaring its own directly connected networks. The `clock rate` command is only needed on the DCE side of each serial link (in this topology: Router0 → Router1 link and Router0 → Router2 link, with Router1 providing clocking towards Router2); the other end is DTE and does not require it. Once all three routers have converged, every laptop in the topology should be able to ping every other laptop across the three LANs, purely thanks to routes learned dynamically via RIP, with no static routing involved.