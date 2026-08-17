# 802.1Q Trunk Configuration Between Two Cisco Switches (VLAN 10 - IT, VLAN 20 - HR)

This project (simulated with Cisco Packet Tracer) demonstrates the configuration of an 802.1Q trunk link between two Layer 2 switches, in order to allow communication between hosts that belong to the same VLANs but are physically connected to different switches.

The goal is to show how, thanks to trunking, VLANs 10 (IT) and 20 (HR) can span multiple switches while keeping the logical traffic segmentation, even though they share the same physical medium (the inter-switch link).

---

## Network Topology

The network consists of 2 switches (`SW-1F` and `SW-2F`), connected to each other via a trunk link, and 12 laptops evenly split between the two VLANs.

| Switch | VLAN | Department | Connected Hosts | IP Range |
|--------|------|------------|------------------|----------|
| SW-1F | 20 | HR | Laptop0, Laptop1, Laptop2 | 192.168.20.1 – .3 |
| SW-1F | 10 | IT | Laptop3, Laptop4, Laptop5 | 192.168.10.1 – .3 |
| SW-2F | 10 | IT | Laptop6, Laptop7, Laptop8 | 192.168.10.4 – .6 |
| SW-2F | 20 | HR | Laptop9, Laptop10, Laptop11 | 192.168.20.4 – .6 |

Both VLANs are therefore distributed across both switches: this is the typical scenario that makes a trunk link necessary, since without it the tagged traffic of the two VLANs would not be able to cross the link between `SW-1F` and `SW-2F`.

- VLAN 10 – IT → network `192.168.10.0/24`
- VLAN 20 – HR → network `192.168.20.0/24`
- Inter-switch link: FastEthernet3/1 port on both devices, configured as a trunk

---

## CLI Commands Used (on `SW-1F`)

Below is the complete sequence of commands, along with an explanation of each.

### 1. Accessing privileged mode and global configuration mode

```
Switch>enable
Switch#configure terminal
```

- `enable` → moves from user mode (`>`) to privileged mode (`#`), required to view/modify the configuration.
- `configure terminal` (short form: `conf t`) → enters global configuration mode, from which switch parameters (hostname, interfaces, VLANs, etc.) can be changed.

### 2. Setting the hostname

```
Switch(config)#hostname SW-1F
```

- Changes the switch name from `Switch` to `SW-1F`, useful for easily identifying the device in a network with multiple switches (in this case representing the ground-floor switch).

### 3. Creating the VLANs

Before assigning any access port to VLAN 10 or VLAN 20, and before the trunk's allowed-VLAN filter makes any sense, the VLANs must exist in the switch's VLAN database:

```
SW-1F(config)#vlan 10
SW-1F(config-vlan)#name IT
SW-1F(config-vlan)#exit
SW-1F(config)#vlan 20
SW-1F(config-vlan)#name HR
SW-1F(config-vlan)#exit
```

- `vlan 10` / `vlan 20` → creates the VLANs and enters VLAN configuration sub-mode.
- `name IT` / `name HR` → assigns a human-readable label to each VLAN, purely for administrative clarity.

### 4. Configuring the access ports

Each group of laptops must be assigned to its VLAN on an access port.

```
SW-1F(config)#interface range Fa0/1-3
SW-1F(config-if-range)#switchport mode access
SW-1F(config-if-range)#switchport access vlan 20
SW-1F(config-if-range)#exit

SW-1F(config)#interface range Fa0/4-6
SW-1F(config-if-range)#switchport mode access
SW-1F(config-if-range)#switchport access vlan 10
SW-1F(config-if-range)#exit
```

- `interface range` → allows configuring multiple consecutive ports at once, instead of repeating the same commands one by one.
- `switchport mode access` → forces the port into access mode, so it does not stay in the default dynamic negotiation state (`dynamic auto`/`desirable`), which is unreliable in Packet Tracer.
- `switchport access vlan X` → assigns the port to the specified VLAN.

The same logic applies on `SW-2F`, but with the VLAN assignments reversed (Fa0/1-3 → VLAN 10, Fa0/4-6 → VLAN 20), following the IP/host table above.

### 5. Entering the interface and configuring the trunk

```
SW-1F(config)#interface Fa3/1
SW-1F(config-if)#switchport mode trunk
```

- `interface Fa3/1` → enters configuration mode for the FastEthernet3/1 interface, the one used to connect `SW-1F` to `SW-2F`.
- `switchport mode trunk` → sets the port to trunk mode, allowing it to carry tagged traffic from multiple VLANs (802.1Q) at the same time, instead of being associated with a single VLAN like an access port.

### 6. Restricting the VLANs allowed on the trunk

```
SW-1F(config-if)#switchport trunk allowed vlan 10,20
```

- `switchport trunk allowed vlan 10,20` → restricts the traffic crossing the trunk exclusively to VLANs 10 and 20, preventing unnecessary VLANs (e.g. the default VLAN 1 or others not relevant to this link) from being forwarded. This is considered security best practice, as it reduces the attack surface and unnecessary broadcast traffic on the link.
- Note the difference between comma and hyphen: `10,20` means only VLAN 10 and VLAN 20. `10-20` would mean every VLAN from 10 to 20 inclusive (10, 11, 12 … 20), which is not what this topology needs. Use the comma form here.

### 7. Verifying the trunk

```
SW-1F#show interfaces trunk
```

This displays, for each trunk port, its mode, encapsulation type, native VLAN, and the list of VLANs actually allowed and active on the trunk. This is the standard way to validate a trunk configuration and is useful as supporting evidence for the project report.

---

## Final Configuration Summary on `SW-1F`

```
hostname SW-1F
!
vlan 10
 name IT
vlan 20
 name HR
!
interface range FastEthernet0/1-3
 switchport mode access
 switchport access vlan 20
!
interface range FastEthernet0/4-6
 switchport mode access
 switchport access vlan 10
!
interface FastEthernet3/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20
```

## Final Configuration Summary on `SW-2F` (mirrored, VLANs reversed)

```
hostname SW-2F
!
vlan 10
 name IT
vlan 20
 name HR
!
interface range FastEthernet0/1-3
 switchport mode access
 switchport access vlan 10
!
interface range FastEthernet0/4-6
 switchport mode access
 switchport access vlan 20
!
interface FastEthernet3/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20
```

The trunk configuration must be mirrored on the Fa3/1 port of `SW-2F` for the link to work correctly on both sides. The access-port VLAN assignments, however, mirror the host distribution table: VLAN 10 on Fa0/1-3 and VLAN 20 on Fa0/4-6 on `SW-2F`, opposite to `SW-1F`.