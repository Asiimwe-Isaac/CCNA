

### What is an IPv4 Address?

An **IPv4 address** is a **logical address** used at **Layer 3 (Network Layer)** to identify devices on a network and enable routing across different networks.

- It is **32 bits** long (4 bytes).
- Written in **dotted decimal notation**: four numbers (octets) separated by dots.
- Each octet ranges from **0 to 255**.

**Example**: `192.168.1.10`

In binary it looks like this:  
`11000000.10101000.00000001.00001010`

### Structure of an IPv4 Address

Every IPv4 address is divided into two parts by a **subnet mask**:

- **Network Portion** — Identifies the **network** (or subnet) the device belongs to.
- **Host Portion** — Identifies the **specific device** (host) on that network.

**Example with subnet mask**:
- IP Address: `192.168.1.10`
- Subnet Mask: `255.255.255.0` (or /24 in CIDR)

Here, the **first three octets** (`192.168.1`) = Network part  
The **last octet** (`10`) = Host part

Devices on the **same network** can communicate directly using ARP + MAC addresses (as we saw earlier).  
To communicate with a device on a **different network**, traffic must go through a **router** (default gateway).

### Subnet Mask and CIDR Notation

The **subnet mask** tells the device where the network portion ends and the host portion begins.

- Common subnet mask: `255.255.255.0` → **/24** (24 bits for network)
- In **CIDR** (Classless Inter-Domain Routing) notation: Write the prefix length after a slash.  
  Example: `192.168.1.10/24`

**Popular CIDR examples**:

| CIDR   | Subnet Mask          | Usable Hosts per Subnet | Typical Use                  |
|--------|----------------------|--------------------------|------------------------------|
| /8     | 255.0.0.0            | ~16 million              | Very large networks          |
| /16    | 255.255.0.0          | ~65,000                  | Medium networks              |
| /24    | 255.255.255.0        | 254                      | Most LANs (very common)      |
| /30    | 255.255.255.252      | 2                        | Point-to-point links (routers)|
| /32    | 255.255.255.255      | 1 (single host)          | Loopback or specific routes  |

**Rule**: You cannot use the **all-zeroes** host address (network ID) or the **all-ones** host address (broadcast address) for a real device.

### IPv4 Address Classes (Legacy – Classful Addressing)

Originally, IPv4 addresses were divided into classes based on the first few bits:

| Class | First Octet Range | Default Mask | Purpose                     | Example                  |
|-------|-------------------|--------------|-----------------------------|--------------------------|
| A     | 1 – 126           | /8           | Very large networks         | 10.0.0.0/8               |
| B     | 128 – 191         | /16          | Large organizations         | 172.16.0.0/16            |
| C     | 192 – 223         | /24          | Small networks              | 192.168.1.0/24           |
| D     | 224 – 239         | -            | Multicast (group traffic)   | 224.0.0.5 (OSPF)         |
| E     | 240 – 255         | -            | Experimental / Reserved     | -                        |

**Note**: Classful addressing is mostly obsolete. Today we use **CIDR** and **VLSM** (Variable Length Subnet Masking) for flexible subnetting.

### Special Types of IPv4 Addresses

1. **Private IP Addresses** (RFC 1918) – Not routable on the public Internet  
   - 10.0.0.0 – 10.255.255.255          (`10.0.0.0/8`)
   - 172.16.0.0 – 172.31.255.255       (`172.16.0.0/12`)
   - 192.168.0.0 – 192.168.255.255     (`192.168.0.0/16`)  
   → Used inside homes, offices, schools, etc. NAT is used to access the Internet.

2. **Public IP Addresses** – Routable on the Internet (assigned by ISPs).

3. **Loopback Address**  
   - `127.0.0.1` (or entire `127.0.0.0/8`)  
   - Used for testing the local TCP/IP stack on the same device.  
   - In Packet Tracer: `ping 127.0.0.1` always works if TCP/IP is installed.

4. **Default Gateway**  
   - The IP address of the router interface on your local network.  
   - Devices send traffic destined for other networks to this address.

5. **Broadcast Address**  
   - All hosts in a subnet (e.g., `192.168.1.255` for /24 subnet).

### How to Configure IPv4 in Packet Tracer

On a **PC**:
- Desktop tab → IP Configuration → Static → Enter IP, Subnet Mask, Default Gateway.

On a **Cisco Router** (example):
```bash
Router> enable
Router# configure terminal
Router(config)# interface GigabitEthernet0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit
Router# copy run start
```

Then verify:
```bash
Router# show ip interface brief
Router# ping 192.168.1.10
```

### Quick Summary

- IPv4 = 32-bit logical address (Layer 3).
- Written as A.B.C.D (dotted decimal).
- Divided by subnet mask into Network + Host portions.
- Same network → Use ARP + MAC for direct delivery.
- Different network → Send to Default Gateway (router).
- Most common in labs: `192.168.x.x/24` (private) and `/30` for router links.

Here's a **clear and practical explanation** of **IPv4 Addressing** focusing on:

- How to calculate the **maximum number of hosts**
- How to find the **first usable address** and **last usable address**
- The **IPv4 address classes** (Classful addressing)

This is very important for Packet Tracer labs and CCNA.

### 1. General Formula for Usable Hosts (Most Important)

For **any** IPv4 network (classful or classless):

- Total addresses in the subnet = **2^(number of host bits)**
- **Maximum usable hosts** = **2^(host bits) - 2**

**Why subtract 2?**
- One address is reserved as the **Network ID** (all host bits = 0) → cannot be assigned to a device.
- One address is reserved as the **Broadcast address** (all host bits = 1) → cannot be assigned to a device.

**Example** (most common in labs):
- IP: `192.168.1.0` with subnet mask `255.255.255.0` (/24)
- Host bits = 8 (last octet)
- Total addresses = 2^8 = **256**
- Usable hosts = 256 - 2 = **254**

### 2. First Usable Address & Last Usable Address

- **Network Address** (first address) → All host bits = **0** → Not usable
- **First usable host** → Network Address + **1**
- **Last usable host** → Broadcast Address - **1**
- **Broadcast Address** (last address) → All host bits = **1** → Not usable

**Practical Example** (Class C style – /24):

Network: `192.168.10.0/24`

- Network Address: `192.168.10.0` (not usable)
- **First usable**: `192.168.10.1`
- **Last usable**: `192.168.10.254`
- Broadcast Address: `192.168.10.255` (not usable)
- Maximum usable hosts: **254**

Another example (/28 subnet – common for small segments):

Network: `192.168.1.0/28`

- Total addresses = 2^(32-28) = 16
- Usable hosts = 16 - 2 = **14**
- First usable: `192.168.1.1`
- Last usable: `192.168.1.14`
- Broadcast: `192.168.1.15`

### 3. IPv4 Address Classes (Classful Addressing)

In the old **classful** system, the class was determined by the **first octet** (first few bits). Each class had a **default subnet mask** and a fixed number of host bits.

Here is the complete table:

| Class | First Octet Range | Default Subnet Mask | CIDR | Network Bits | Host Bits | Max Usable Hosts          | Purpose                          | Example Network      |
|-------|-------------------|---------------------|------|--------------|-----------|---------------------------|----------------------------------|----------------------|
| A     | 1 – 126           | 255.0.0.0           | /8   | 8            | 24        | 16,777,214                | Very large networks (e.g., ISPs) | 10.0.0.0/8           |
| B     | 128 – 191         | 255.255.0.0         | /16  | 16           | 16        | 65,534                    | Large organizations              | 172.16.0.0/16        |
| C     | 192 – 223         | 255.255.255.0       | /24  | 24           | 8         | 254                       | Small to medium networks         | 192.168.1.0/24       |
| D     | 224 – 239         | -                   | -    | -            | -         | -                         | Multicast (group communication)  | 224.0.0.5 (OSPF)     |
| E     | 240 – 255         | -                   | -    | -            | -         | -                         | Experimental / Reserved          | -                    |

**Notes**:
- Class A, B, and C are for **unicast** (normal device addressing).
- **Private IP ranges** (RFC 1918):
  - Class A: 10.0.0.0 – 10.255.255.255
  - Class B: 172.16.0.0 – 172.31.255.255
  - Class C: 192.168.0.0 – 192.168.255.255
- 127.0.0.0/8 is reserved for **loopback** (`127.0.0.1`).

### How to Find First/Last Usable and Max Hosts in Classful Networks

**Class C Example** (default mask):
- Network: `192.168.5.0`
- Default Mask: `/24` or `255.255.255.0`
- Max usable hosts: **254**
- First usable: `192.168.5.1`
- Last usable: `192.168.5.254`

**Class B Example**:
- Network: `172.16.0.0`
- Default Mask: `/16` or `255.255.0.0`
- Max usable hosts: **65,534**
- First usable: `172.16.0.1`
- Last usable: `172.16.255.254`

**Class A Example**:
- Network: `10.0.0.0`
- Default Mask: `/8` or `255.0.0.0`
- Max usable hosts: **16,777,214**
- First usable: `10.0.0.1`
- Last usable: `10.255.255.254`

### Quick Tips for Packet Tracer

1. When you assign an IP to a router/PC, never use the **network address** or **broadcast address**.
2. The **default gateway** for hosts is usually the **first usable** address on the router interface (e.g., `.1`).
3. In modern networks, we mostly use **CIDR** and **subnetting** (VLSM) instead of strict classful masks.

