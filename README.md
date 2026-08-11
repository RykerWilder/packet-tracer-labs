# Cisco Packet Tracer – Useful Commands Cheat Sheet

A quick reference of the most useful commands for working with PCs, laptops, routers, and switches in Cisco Packet Tracer.

---

## PC / Laptop (Desktop → Command Prompt)

### Check IP configuration
```
ipconfig              → shows IP address, subnet mask, default gateway
ipconfig /all          → also shows MAC address, DNS servers, DHCP server
```

### Test connectivity
```
ping <ip>              → checks if a host responds
ping -t <ip>            → continuous ping (Ctrl+C to stop)
tracert <ip>            → shows the path (hop-by-hop) to a destination
```

### Check ARP table and connections
```
arp -a                  → shows the ARP table (IP ↔ MAC mapping)
netstat                 → shows active connections
nslookup <domain>        → queries a DNS server
```

### Renew DHCP lease
```
ipconfig /release       → releases the current IP address
ipconfig /renew          → requests a new IP address from the DHCP server
```

---

## Router & Switch (IOS CLI)

### Basic navigation
```
enable                  → enters privileged EXEC mode
configure terminal       → enters global configuration mode (conf t)
exit / end                → exits the current mode
```

### View device status
```
show running-config      → displays the current configuration (sh run)
show ip interface brief   → shows status/IP of all interfaces (sh ip int br)
show interfaces           → detailed info on all interfaces
show version               → shows IOS version, hardware, uptime
```

### Interface configuration
```
interface <name>          → enters a specific interface (e.g. interface fastEthernet 0/0)
ip address <ip> <mask>      → assigns an IP address to the interface
no shutdown                  → enables the interface (brings it up)
shutdown                      → disables the interface
```

### Switch-specific commands
```
show mac address-table       → shows the MAC address-to-port table
show vlan brief                → lists configured VLANs
vlan <number>                    → creates or enters a VLAN
switchport mode access|trunk      → sets the port mode (access or trunk)
```

### Router-specific commands
```
show ip route                  → displays the routing table
ip route <network> <mask> <next-hop>  → adds a static route
show ip dhcp binding             → shows IP addresses leased by the DHCP server (if the router acts as one)
```

### Save configuration
```
copy running-config startup-config   → saves the current config (shortcut: "wr")
```

---

## Troubleshooting & Debugging

```
debug ip dhcp server events     → shows DHCP requests in real time (on the router)
show ip dhcp pool                 → verifies the configured DHCP pool
show cdp neighbors                  → shows directly connected Cisco devices
```

---

*Part of the [packet-tracer-labs](.) repository — networking labs built with Cisco Packet Tracer.*