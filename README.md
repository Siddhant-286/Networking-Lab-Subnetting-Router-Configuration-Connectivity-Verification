# Project Title

A brief description of what this project does and who it's for

# Cisco Networking Lab – Subnetting, Router Configuration & Connectivity Verification

## Problem Statement

Design and configure a multi-site Cisco network using the allocated network **192.168.1.0/24**.

The network must initially be divided into four subnets:

1. **Subnet 1** – Site 1 LAN
2. **Subnet 2** – Point-to-point link between R1 and the Internet Router
3. **Subnet 3** – Site 2 LAN
4. **Subnet 4** – Point-to-point link between R2 and the Internet Router

The network must then be extended by further subnetting **192.168.1.64/26** to create subnets capable of supporting **8 hosts per subnet**. The first newly created subnet will be assigned to **Site 3**.

All devices must be manually configured with appropriate IP addresses, subnet masks, default gateways, and router interfaces. The router in Site 3 must use the **last usable IP address** of its subnet, while hosts must use the **first available IP addresses**.

Finally, the last subnet created from **192.168.1.64/26** must be further subnetted using **/30 masks** to create point-to-point subnets for the serial links.

The completed network must provide end-to-end connectivity, and the PCs must be able to access **cisco.com** and **facebook.com** through their web browsers.

---

# Network Requirements

### Base Network

```text
Network: 192.168.1.0/24
```

The /24 network is divided into four initial subnets.

| Subnet   | Purpose              |
| -------- | -------------------- |
| Subnet 1 | Site 1 LAN           |
| Subnet 2 | R1 ↔ Internet Router |
| Subnet 3 | Site 2 LAN           |
| Subnet 4 | R2 ↔ Internet Router |

---

# Part 1 – Subnet 192.168.1.0/24

The /24 network needs to be divided into **4 equal subnets**.

Borrow:

```text
2 bits
```

Therefore:

```text
/24 → /26
```

Subnet mask:

```text
255.255.255.192
```

The four subnets are:

| Subnet | Network          | Usable Host Range             | Broadcast     |
| ------ | ---------------- | ----------------------------- | ------------- |
| 1      | 192.168.1.0/26   | 192.168.1.1 – 192.168.1.62    | 192.168.1.63  |
| 2      | 192.168.1.64/26  | 192.168.1.65 – 192.168.1.126  | 192.168.1.127 |
| 3      | 192.168.1.128/26 | 192.168.1.129 – 192.168.1.190 | 192.168.1.191 |
| 4      | 192.168.1.192/26 | 192.168.1.193 – 192.168.1.254 | 192.168.1.255 |

Assign them according to the topology:

```text
192.168.1.0/26     → Site 1
192.168.1.64/26    → R1 ↔ Internet Router
192.168.1.128/26   → Site 2
192.168.1.192/26   → R2 ↔ Internet Router
```

---

# Part 2 – Extend the Network for Site 3

The network:

```text
192.168.1.64/26
```

must be further subnetted to support **8 hosts per subnet**.

For 8 usable hosts:

```text
2^4 - 2 = 14 hosts
```

Therefore, use:

```text
/28
```

Subnet mask:

```text
255.255.255.240
```

The /26 network produces four /28 subnets:

| Subnet | Network          | Usable Hosts | Broadcast |
| ------ | ---------------- | ------------ | --------- |
| 1      | 192.168.1.64/28  | .65 – .78    | .79       |
| 2      | 192.168.1.80/28  | .81 – .94    | .95       |
| 3      | 192.168.1.96/28  | .97 – .110   | .111      |
| 4      | 192.168.1.112/28 | .113 – .126  | .127      |

### Site 3 Allocation

The **first new subnet** is allocated to Site 3:

```text
Network: 192.168.1.64/28
```

Router:

```text
192.168.1.78
```

This is the **last usable IP address**.

Hosts are assigned starting from the first usable IP:

```text
PC1 → 192.168.1.65
PC2 → 192.168.1.66
PC3 → 192.168.1.67
...
```

Default gateway for all Site 3 hosts:

```text
192.168.1.78
```

---

# Part 3 – Create /30 Serial Subnets

The **last subnet** obtained from the /26 network is:

```text
192.168.1.112/28
```

This subnet is further divided using:

```text
/30
```

Subnet mask:

```text
255.255.255.252
```

A /30 subnet provides:

```text
4 total addresses
2 usable host addresses
```

The /28 network produces four /30 subnets:

| Subnet | Network          | Usable IPs  | Broadcast |
| ------ | ---------------- | ----------- | --------- |
| 1      | 192.168.1.112/30 | .113 – .114 | .115      |
| 2      | 192.168.1.116/30 | .117 – .118 | .119      |
| 3      | 192.168.1.120/30 | .121 – .122 | .123      |
| 4      | 192.168.1.124/30 | .125 – .126 | .127      |

These /30 networks can be allocated to the required point-to-point serial links.

Example:

```text
R1 ↔ Internet Router
192.168.1.112/30

R2 ↔ Internet Router
192.168.1.116/30
```

The two usable addresses in each /30 subnet are assigned to the two routers.

---

# Part 4 – Router Configuration

Configure the appropriate interfaces on each router with:

- IP address
- Subnet mask
- Interface activation using `no shutdown`
- Serial interface configuration where required
- Appropriate routing configuration

Example:

```text
Router(config)# interface g0/0
Router(config-if)# ip address <IP> <MASK>
Router(config-if)# no shutdown
```

For serial interfaces:

```text
Router(config)# interface s0/0/0
Router(config-if)# ip address <IP> 255.255.255.252
Router(config-if)# no shutdown
```

If the router is the DCE side of the serial connection:

```text
Router(config-if)# clock rate 64000
```

---

# Part 5 – PC Configuration

Each PC must be manually configured with:

```text
IP Address
Subnet Mask
Default Gateway
DNS Server
```

Example for a Site 3 PC:

```text
IP Address:      192.168.1.65
Subnet Mask:     255.255.255.240
Default Gateway: 192.168.1.78
```

---

# Part 6 – Routing

Configure routing between the different networks so that packets can travel between:

```text
Site 1
   ↓
R1
   ↓
Internet Router
   ↓
R2
   ↓
Site 2 / Site 3
```

Depending on the topology, static routes or a dynamic routing protocol can be used.

Verify the routing table using:

```text
show ip route
```

---

# Part 7 – Verification

First verify interface status:

```text
show ip interface brief
```

All required interfaces should be:

```text
Status: up
Protocol: up
```

Test local connectivity:

```text
ping <default-gateway>
```

Test router-to-router connectivity:

```text
ping <remote-router-IP>
```

Test end-to-end connectivity:

```text
ping <remote-PC-IP>
```

Use traceroute to identify the path:

```text
traceroute <destination-IP>
```

Finally, open the PC's web browser in Packet Tracer and verify access to:

```text
cisco.com
facebook.com
```

---

# Final Verification Checklist

- [x] 192.168.1.0/24 divided into four /26 networks
- [x] Site 1 configured
- [x] R1 ↔ Internet Router link configured
- [x] Site 2 configured
- [x] R2 ↔ Internet Router link configured
- [x] 192.168.1.64/26 further subnetted into /28 networks
- [x] First /28 allocated to Site 3
- [x] Site 3 router uses the last usable IP
- [x] Site 3 hosts use the first available IPs
- [x] Last /28 subnet further divided into /30 networks
- [x] /30 networks assigned to serial links
- [x] Router interfaces configured and activated
- [x] Routing configured
- [x] Ping connectivity verified
- [x] Cisco.com accessible
- [x] Facebook.com accessible

## Objective

The objective of this lab is to demonstrate practical understanding of **subnetting, VLSM, IP addressing, router interface configuration, point-to-point /30 networks, routing, and end-to-end connectivity** in a Cisco Packet Tracer environment.
