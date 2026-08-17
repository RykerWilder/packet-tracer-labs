# DNS Server Configuration in Cisco Packet Tracer

This project (simulated with Cisco Packet Tracer) demonstrates how to configure a generic server as a DNS server, publish a simple web page on it, and configure two laptops so they can reach the server by its domain name instead of its IP address.

Unlike switch/router configuration, a Packet Tracer server and PC are configured mostly through the graphical interface (Config / Services / Desktop tabs), not through CLI commands. Below is the full sequence of steps, in order, with the exact tabs and fields to use.

---

## Network Topology

| Device | Interface | IP Address | Subnet Mask | Role |
|--------|-----------|------------|-------------|------|
| DNS_Server | Fa0 | 192.168.1.100 | 255.255.255.0 | DNS + HTTP server, domain www.rykerwilder.com |
| Switch0 | Fa2/1 | - | - | Connects to DNS_Server |
| Switch0 | Fa0/1 | - | - | Connects to Laptop0 |
| Switch0 | Fa1/1 | - | - | Connects to Laptop1 |
| Laptop0 | Fa0 | 192.168.1.1 | 255.255.255.0 | Client |
| Laptop1 | Fa0 | 192.168.1.2 | 255.255.255.0 | Client |

All devices are on the same network `192.168.1.0/24`, connected through `Switch0`.

---

## Step 1: Rename the server

1. Click on `DNS_Server` in the topology.
2. Open the **Config** tab.
3. In the **Global Settings** section, find the **Display Name** field.
4. Replace the default name (e.g. `Server0`) with `DNS_Server`.

This only changes the label shown in the topology; it does not affect any network setting.

## Step 2: Assign the IP address to the server

1. With `DNS_Server` still selected, go to the **Config** tab.
2. Select the interface `FastEthernet0` from the left menu.
3. Set:
   - **IP Address**: `192.168.1.100`
   - **Subnet Mask**: `255.255.255.0`
4. Leave **DHCP** unchecked, since the server needs a fixed, predictable address (a static IP) rather than one from a DHCP pool.

## Step 3: Enable the DNS service

1. Go to the **Services** tab of `DNS_Server`.
2. Select **DNS** from the left menu.
3. Set the **DNS Service** switch to **On**.
4. In the resource record fields, enter:
   - **Name**: `www.rykerwilder.com`
   - **Type**: `A Record`
   - **Address**: `192.168.1.100`
5. Click **Add** to insert the record into the table, then confirm it appears listed below.

This step is what actually maps the domain name to the server's IP address. Without an `A Record`, laptops trying to reach `www.rykerwilder.com` would get no resolution, even if the server itself is reachable by IP.

## Step 4: Publish the index.html page

1. Still in the **Services** tab of `DNS_Server`, select **HTTP** from the left menu.
2. Make sure the **HTTP** (and optionally **HTTPS**) service is set to **On**.
3. In the file list, select the existing `index.html` file and click **Edit**.
4. Replace the placeholder HTML content with your own, for example:

```html
<html>
<head>
<title>Ryker Wilder</title>
</head>
<body>
<h1>Welcome to www.rykerwilder.com</h1>
<p>This page is served by DNS_Server in Packet Tracer.</p>
</body>
</html>
```

5. Save the changes.

This is the page that laptops will see when they open a browser and type `www.rykerwilder.com` or `192.168.1.100`.

## Step 5: Confirm the DNS name is correctly associated

This step is really a verification of Step 3, but it's worth doing separately before moving to the clients:

1. Go back to the **Services > DNS** tab.
2. Check that the table shows:

| Name | Type | Address |
|------|------|---------|
| www.rykerwilder.com | A Record | 192.168.1.100 |

3. If the row is missing or the address is wrong, correct the fields and click **Add** (or **Save**, depending on the Packet Tracer version) again.

## Step 6: Configure the IP addresses on the laptops

For each laptop:

1. Click on `Laptop0`.
2. Open the **Desktop** tab.
3. Click **IP Configuration**.
4. Set:
   - **IP Address**: `192.168.1.1`
   - **Subnet Mask**: `255.255.255.0`
   - **DNS Server**: `192.168.1.100`
5. Repeat the same steps for `Laptop1`, using:
   - **IP Address**: `192.168.1.2`
   - **Subnet Mask**: `255.255.255.0`
   - **DNS Server**: `192.168.1.100`

The **DNS Server** field is the critical part here: it tells the laptop which device to query when it needs to resolve a domain name into an IP address. Both laptops must point to `192.168.1.100`, since that is the address of `DNS_Server`.

## Step 7: Test the configuration

1. On `Laptop0`, open the **Desktop** tab and launch **Web Browser** (or **Command Prompt**).
2. In the browser address bar, type `www.rykerwilder.com` and press Go.
3. The page you edited in Step 4 should load.
4. Optionally, from **Command Prompt**, run:

```
ping www.rykerwilder.com
```

If the DNS record and IP configuration are correct, the ping resolves the name to `192.168.1.100` and replies successfully. Repeat the same test from `Laptop1`.

---

## Summary Table

| Step | Where | What to do |
|------|-------|-------------|
| 1 | DNS_Server > Config > Global Settings | Rename to DNS_Server |
| 2 | DNS_Server > Config > FastEthernet0 | Assign IP 192.168.1.100 / 255.255.255.0 |
| 3 | DNS_Server > Services > DNS | Turn DNS on, add A Record www.rykerwilder.com → 192.168.1.100 |
| 4 | DNS_Server > Services > HTTP | Edit index.html with the desired page content |
| 5 | DNS_Server > Services > DNS | Verify the record is listed correctly |
| 6 | Laptop0 / Laptop1 > Desktop > IP Configuration | Assign static IP, subnet mask, and DNS server 192.168.1.100 |
| 7 | Laptop0 / Laptop1 > Desktop > Web Browser or Command Prompt | Test by browsing or pinging www.rykerwilder.com |