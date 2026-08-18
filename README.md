# Packet Tracer Labs

A collection of networking projects created with Cisco Packet Tracer. Each project is a .pkt file with a working topology, documented in detail in a dedicated markdown file in the [`docs/`](docs) folder.

## Purpose of the Repo

This repository serves as:

- **Practical Portfolio** of networking labs completed during the course.
- **Reference Archive** to return to for reviewing previously covered configurations (DHCP, DNS, VLAN/trunking, and upcoming topics).
- **Progressive Learning Path**, from basic concepts (addressing, network services) to more complex topologies (dynamic routing, ACLs, business simulations).

## Projects

| Project | Description | .pkt File | Documentation |
|---|---|---|---|
| **DHCP Server Configuration** | Configuring a DHCP server to automatically assign IP addresses, subnet masks, gateways, and DNS to LAN clients. | [`.pkt`](DHCP_Server_Configuration.pkt) | [`docs/DHCP_Server_Configuration.md`](docs/DHCP_Server_Configuration.md) |
| **DNS Server Configuration** | Configuring a DNS server with an A record and an HTTP web page so that clients reach the server by domain name instead of IP. | [`.pkt`](DNS_Server_Configuration.pkt) | [`docs/DNS_Server_Configuration.md`](docs/DNS_Server_Configuration.md) |
| **VLAN Trunk Configuration** | Configuring an 802.1Q trunk between two switches to allow hosts on the same VLAN (IT and HR) to communicate when physically connected to different switches. | [`.pkt`](VLAN_Trunk_Configuration.pkt) | [`docs/VLAN_Trunk-Configuration.md`](docs/VLAN_Trunk-Configuration.md) |

> The table will be updated as new projects are added to the repository.

## License

This project is distributed under the [MIT](LICENSE) license.