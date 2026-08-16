# 802.1Q Trunk Configuration Between Two Cisco Switches (VLAN 10 - IT, VLAN 20 - HR)

This project (simulated with **Cisco Packet Tracer**) demonstrates the configuration of an **802.1Q trunk** link between two Layer 2 switches, in order to allow communication between hosts that belong to the same VLANs but are physically connected to different switches.

The goal is to show how, thanks to trunking, VLANs **10 (IT)** and **20 (HR)** can span multiple switches while keeping the logical traffic segmentation, even though they share the same physical medium (the inter-switch link).

---

## 🖧 Network Topology

The network consists of **2 switches** (`SW-1F` and `SW-2F`), connected to each other via a **trunk** link, and **12 laptops** evenly split between the two VLANs.

| Switch | VLAN | Department | Connected Hosts | IP Range |
|--------|------|------------|------------------|----------|
| SW-1F | 20 | HR | Laptop0, Laptop1, Laptop2 | 192.168.20.1 – .3 |
| SW-1F | 10 | IT | Laptop3, Laptop4, Laptop5 | 192.168.10.1 – .3 |
| SW-2F | 10 | IT | Laptop6, Laptop7, Laptop8 | 192.168.10.4 – .6 |
| SW-2F | 20 | HR | Laptop9, Laptop10, Laptop11 | 192.168.20.4 – .6 |

Both VLANs are therefore **distributed across both switches**: this is the typical scenario that makes a trunk link necessary, since without it the tagged traffic of the two VLANs would not be able to cross the link between `SW-1F` and `SW-2F`.

- **VLAN 10 – IT** → network `192.168.10.0/24`
- **VLAN 20 – HR** → network `192.168.20.0/24`
- Inter-switch link: **FastEthernet3/1 port** on both devices, configured as a **trunk**

---

## CLI Commands Used (on `SW-1F`)

Below is the sequence of commands executed on the switch, along with an explanation of each.

### 1. Accessing privileged mode and global configuration mode

```
Switch>enable
Switch#conf t
```

- `enable` → moves from user mode (`>`) to **privileged mode** (`#`), required to view/modify the configuration.
- `conf t` (short for `configure terminal`) → enters **global configuration mode**, from which switch parameters (hostname, interfaces, VLANs, etc.) can be changed.

### 2. Setting the hostname

```
Switch(config)#hostname SW-1F
```

- Changes the switch name from `Switch` to `SW-1F`, useful for easily identifying the device in a network with multiple switches (in this case representing the ground-floor switch).

### 4. Entering the interface and configuring the trunk

```
SW-1F(config)#interface Fa3/1
SW-1F(config-if)#switchport mode trunk
```

- `interface Fa3/1` → enters configuration mode for the single **FastEthernet3/1** interface, the one used to connect `SW-1F` to `SW-2F`.
- `switchport mode trunk` → sets the port to **trunk** mode, allowing it to carry tagged traffic from **multiple VLANs** (802.1Q) at the same time, instead of being associated with a single VLAN like an *access* port.

### 5. Restricting the VLANs allowed on the trunk

```
SW-1F(config-if)#switchport trunk allowed vlan 10,20
```

- The first command (`allo`) is an incomplete attempt, later corrected by the second one.
- `switchport trunk allowed vlan 10-20` → restricts the traffic crossing the trunk **exclusively to VLANs 10 and 20**, preventing unnecessary VLANs (e.g. the default VLAN 1 or others not relevant to this link) from being forwarded. This is considered **security best practice**, as it reduces the attack surface and unnecessary broadcast traffic on the link.

---

## Final Configuration Summary on `SW-1F` (Fa3/1 interface)

```
interface FastEthernet3/1
 switchport trunk allowed vlan 10,20
 switchport mode trunk
```

The same configuration must be mirrored on the `Fa3/1` port of `SW-2F`, so that the trunk link works correctly on both sides.