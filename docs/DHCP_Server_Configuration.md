# DHCP Server Configuration in Cisco Packet Tracer

This project (simulated with Cisco Packet Tracer) demonstrates how to configure a DHCP service so that client devices on a LAN automatically receive an IP address, subnet mask, default gateway, and DNS server, instead of being configured manually.

---

## Network Topology

| Device | Interface | IP Address | Subnet Mask | Role |
|--------|-----------|------------|-------------|------|
| DHCP_Server | Fa0 | Static IP (e.g. 192.168.1.100) | 255.255.255.0 | DHCP server |
| Switch0 | - | - | - | Connects server and clients |
| PC / Laptops | Fa0 | Assigned dynamically | Assigned dynamically | DHCP clients |

The server is assigned a static IP address, while all other hosts are configured to obtain their addressing automatically via DHCP.

---

## Configuration Steps

### Step 1: Assign a static IP to the server

1. Select `DHCP_Server` in the topology.
2. Open the **Config** tab.
3. Select the **FastEthernet0** interface.
4. Assign a static **IP Address** and **Subnet Mask** (the server itself cannot use DHCP, since it's the one providing it).

### Step 2: Enable the DHCP service

1. Go to the **Services** tab of `DHCP_Server`.
2. Select **DHCP** from the left menu.
3. Set the **DHCP Service** switch to **On**.
4. Configure the pool:
   - **Pool Name**
   - **Default Gateway**
   - **DNS Server** (optional)
   - **Starting IP Address**
   - **Subnet Mask**
   - **Maximum number of users**
5. Click **Save**.

### Step 3: Configure clients as DHCP clients

For each PC/Laptop:

1. Open the **Desktop** tab.
2. Click **IP Configuration**.
3. Select **DHCP** instead of Static.

The device will send a DHCP request and receive an IP address from the pool configured on the server.

### Step 4: Verify the configuration

From a client's **Command Prompt**:

```
ipconfig /all
```

Confirms the IP address, subnet mask, gateway, and DHCP server address received automatically.

On the server side, checking the DHCP service page shows the list of leased addresses currently in use.