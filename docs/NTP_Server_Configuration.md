# NTP Server Configuration

## Project Description

The project simulates a LAN network in which an NTP (Network Time Protocol) server is connected, through a switch, to three router clients. The purpose of the exercise is to configure the NTP server as the authoritative time source of the network and to configure each router client to synchronize its system clock with the server, ensuring that all devices in the network share a consistent and accurate time reference.

### Network Topology

- **NTP-Server** (`192.168.10.10`) is connected to **Switch0** port `Fa3/1` via its `Fa0` interface.
- **Switch0** acts as the central connection point of the network and is connected to:
  - **NTP-Client-1** (`192.168.10.11`) via port `Fa0/1` — router interface `Gig0/0/0`
  - **NTP-Client-2** (`192.168.10.12`) via port `Fa1/1` — router interface `Gig0/0/0`
  - **NTP-Client-3** (`192.168.10.13`) via port `Fa2/1` — router interface `Gig0/0/0`

All devices belong to the same `192.168.10.0/24` subnet, so no routing is required between the server and the clients: the switch simply provides Layer 2 connectivity between the NTP server and the three router clients.

---

## NTP (Network Time Protocol)

A protocol used to synchronize the clocks of devices across a network to a common time reference. Accurate and consistent timestamps are essential for logging, troubleshooting, authentication protocols, and the correct interpretation of event sequences across multiple devices.

### How NTP Synchronization Works

1. The NTP server is configured as the authoritative time source (the master) for the network, optionally using its own local clock as reference (stratum).
2. Each NTP client sends periodic requests to the server's IP address, asking for the current time.
3. The server responds with its current time value.
4. The client calculates the round-trip delay and offset, then gradually adjusts its own clock to match the server, rather than changing it abruptly.
5. Over time, clients keep polling the server to remain synchronized, correcting for clock drift.

---

## Device Configuration

### NTP-Server

The server is configured with a static IP address (`192.168.10.10/24`) and its NTP service is enabled, acting as the master time source for the network. Since it has no external reference clock configured, it uses its own local system clock as the time source that clients will synchronize to.

### NTP-Client-1 / NTP-Client-2 / NTP-Client-3

Each router is configured with the appropriate IP address on its `Gig0/0/0` interface (`192.168.10.11`, `192.168.10.12`, `192.168.10.13`), and NTP client functionality is enabled pointing to the server's address:

```
configure terminal
ntp server 192.168.10.10
end
```

This command instructs the router to periodically query `192.168.10.10` and synchronize its system clock accordingly. The synchronization status and current time source can be verified with:

```
show ntp status
show ntp associations
```

---

## Functional Summary

| Device | Role | Interface | IP Address |
|---|---|---|---|
| NTP-Server | Time source (master) | Fa0 | 192.168.10.10 |
| NTP-Client-1 | NTP client | Gig0/0/0 | 192.168.10.11 |
| NTP-Client-2 | NTP client | Gig0/0/0 | 192.168.10.12 |
| NTP-Client-3 | NTP client | Gig0/0/0 | 192.168.10.13 |

Once configured, all three router clients synchronize their system clock to the NTP server, ensuring that timestamps across the network (used for logging, debugging, and time-based operations) remain consistent and reliable, regardless of each device's independent hardware clock drift.