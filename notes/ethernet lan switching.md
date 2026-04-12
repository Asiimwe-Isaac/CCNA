Parts of an Ethernet Frame

An Ethernet frame is the basic unit of data transmitted at Layer 2 (Data Link layer) over Ethernet networks. It encapsulates upper-layer data (usually an IP packet) and adds addressing and error-checking information.

Ethernet Frame Structure Overview

The full transmitted frame on the wire includes synchronization fields, but the actual **Ethernet frame** (as processed by switches and NICs) starts from the Destination MAC address.

| Field                          | Size (Bytes) | Purpose                                                                 |
|--------------------------------|--------------|-------------------------------------------------------------------------|
| Preamble                   | 7            | Synchronization pattern (alternating 1s and 0s: 10101010...)            |
| Start Frame Delimiter (SFD)| 1            | Marks the end of preamble and start of the actual frame (10101011)      |
| Destination MAC Address    | 6            | MAC address of the receiving device                                     |
| Source MAC Address         | 6            | MAC address of the sending device                                       |
| Type / Length              | 2            | Indicates the protocol (Type) or length of data                         |
| Data / Payload ( + Pad)    | 46 – 1500    | Actual data from upper layer (IP packet). Padded if less than 46 bytes  |
| Frame Check Sequence (FCS) | 4            | Error detection using CRC (Cyclic Redundancy Check)                     |

Total minimum frame size: 64 bytes (excluding Preamble + SFD)  
Total maximum frame size: 1518 bytes (standard MTU)

After the FCS, there is an **Inter-Frame Gap (IFG)** of 12 bytes (96 bits) of idle time between frames.

Detailed Explanation of Each Field

1. Preamble (7 bytes)  
   - A repeating pattern of `10101010`.  
   - Helps the receiving NIC synchronize its clock with the incoming signal.  
   - Not part of the actual Layer 2 frame (it's at the Physical layer).  
   - Dropped by the receiver after synchronization.

2. Start Frame Delimiter – SFD (1 byte)  
   - Pattern: `10101011` (last two bits are 11).  
   - Signals that the frame content is about to begin.  
   - Also not counted in the official frame size.

3. Destination MAC Address (6 bytes)  
   - The hardware (physical) address of the intended recipient.  
   - Switches use this field to forward the frame to the correct port.  
   - Can be:  
     - **Unicast** (specific device)  
     - **Multicast**  
     - **Broadcast** (FF:FF:FF:FF:FF:FF)

4. Source MAC Address (6 bytes)  
   - The MAC address of the device that sent the frame.  
   - Switches learn and store this in their MAC address table (CAM table).  
   - Always a unicast address.

5. Type / Length Field (2 bytes)  
   - In modern **Ethernet II** frames (most common), this is the EtherType field.  
     - Examples:  
       - `0x0800` → IPv4  
       - `0x86DD` → IPv6  
       - `0x0806` → ARP  
   - If the value is **1500 or less**, it is interpreted as **Length** (IEEE 802.3 format) — tells how many bytes are in the Data field.

6. Data / Payload (46 – 1500 bytes)  
   - This is the actual encapsulated data (usually an IP packet from Layer 3).  
   - Minimum size is **46 bytes**. If the data is smaller, **padding** (zeros) is added to reach 46 bytes.  
   - Maximum is usually 1500 bytes (standard MTU).

7. Frame Check Sequence – FCS (4 bytes)  
   - Also called **CRC** (Cyclic Redundancy Check).  
   - A 32-bit checksum calculated over the entire frame (from Destination MAC to end of Data).  
   - The receiving device recalculates the CRC and compares it with the FCS value.  
   - If they don't match → the frame is discarded (error detected).  
   - This provides error detection but **not** error correction.


Important Notes for Packet Tracer / CCNA

- In Packet Tracer, when you use Simulation mode, you can see Ethernet frames being sent between devices and inspect the fields (especially MAC addresses, Type, and Data).
- Switches forward based on Destination MAC.
- Collisions or errors are detected using the **FCS**.
- Jumbo frames (larger than 1500 bytes) exist in some modern networks but are not standard.

Here's a **clear, complete, and beginner-friendly** explanation of **MAC Addresses** in networking (especially important for Cisco Packet Tracer and CCNA).

### What is a MAC Address?

**MAC** stands for **Media Access Control**.

A **MAC address** is a **unique hardware identifier** assigned to every Network Interface Card (NIC) — whether it's in a computer, laptop, router, switch port, smartphone, or IoT device.

It operates at **Layer 2 (Data Link Layer)** of the OSI model.

### Key Characteristics of MAC Addresses

| Feature                      | Description                                                                 |
|-----------------------------|-----------------------------------------------------------------------------|
| Length                      | 48 bits (6 bytes / 12 hexadecimal digits)                                   |
| Format                      | Usually written as **XX:XX:XX:XX:XX:XX** or **XX-XX-XX-XX-XX-XX**          |
| Example                     | `00:1A:2B:3C:4D:5E`   or   `00-1A-2B-3C-4D-5E`                             |
| Uniqueness                  | Globally unique (assigned by manufacturer)                                  |
| Burned-in Address (BIA)     | Permanently programmed into the hardware by the manufacturer                |
| Changeable?                 | Can be spoofed/changed in software, but the physical one is fixed           |

### Structure of a MAC Address (OUI + NIC)

A MAC address is divided into two main parts:

```
First 3 bytes (24 bits)     Last 3 bytes (24 bits)
┌─────────────────────┐   ┌─────────────────────┐
│       OUI           │   │     NIC Specific    │
└─────────────────────┘   └─────────────────────┘
```

- **OUI (Organizationally Unique Identifier)**  
  First 3 bytes (6 hex digits).  
  Identifies the **manufacturer** (e.g., Cisco, Dell, Intel, TP-Link, Apple).  
  Example: `00:1A:2B` = Cisco Systems.

- **NIC Specific / Device Identifier**  
  Last 3 bytes.  
  Assigned by the manufacturer to make each device unique.

**Full Example**:  
`00:1A:2B:3C:4D:5E`  
→ `00:1A:2B` = Cisco  
→ `3C:4D:5E` = Unique number given by Cisco to this particular device.

### Types of MAC Addresses

1. **Unicast MAC Address** (Most common)  
   - Points to a **single specific device**.  
   - First bit of the first byte is **0**.  
   - Example: `00:1A:2B:3C:4D:5E`

2. **Broadcast MAC Address**  
   - `FF:FF:FF:FF:FF:FF`  
   - Means “send this frame to **all** devices on the local network”.

3. **Multicast MAC Address**  
   - First byte ends with an odd number in binary (least significant bit = 1).  
   - Used for group communication (e.g., video streaming, routing protocols).

### Important Facts about MAC Addresses

- **Layer 2 addressing**: Used for communication **within the same local network** (same LAN / same broadcast domain).
- **Not routable**: Routers do **not** forward frames based on MAC addresses. They strip the old Ethernet header and create a new one.
- **Switches use MAC addresses**: Switches learn and build a **MAC Address Table** (also called CAM table) to forward frames intelligently to the correct port.
- **IP vs MAC**:
  - IP address = Logical address (Layer 3) → can change
  - MAC address = Physical address (Layer 2) → usually fixed

### How MAC Addresses Work in Practice (Packet Tracer View)

When PC1 wants to send data to PC2 on the same network:

1. PC1 checks if the destination IP is on the same subnet.
2. If yes, it needs the **destination MAC address**.
3. It sends an **ARP request** (broadcast) asking: “Who has IP 192.168.1.20? Tell me your MAC.”
4. The destination device replies with its MAC address.
5. PC1 then builds an **Ethernet frame**:
   - Source MAC = PC1’s MAC
   - Destination MAC = PC2’s MAC
   - Data = IP packet

The switch looks at the Destination MAC and forwards the frame only to the correct port (not flooding like a hub).

### Commands to See MAC Addresses in Packet Tracer

On a **Router or Switch**:
```bash
Router# show interfaces
Router# show mac address-table     ← (on switches only)
```

On a **Windows PC** (in Packet Tracer Command Prompt):
```bash
ipconfig /all
```

---

### Quick Summary

- MAC address = **Physical / Hardware address** of a network device.
- 48-bit, written in hexadecimal.
- First half = Manufacturer (OUI), second half = Device ID.
- Used for **local delivery** inside a LAN.
- Switches learn MAC addresses automatically.
- Broadcast MAC = `FF:FF:FF:FF:FF:FF`

Here's a **clear and complete explanation** of **ARP (Address Resolution Protocol)**, building on what we covered about MAC addresses and Ethernet frames.

### What is ARP?

**ARP** stands for **Address Resolution Protocol**.

It is a protocol used in IPv4 networks to **map a logical IP address (Layer 3)** to a **physical MAC address (Layer 2)**.

Why do we need it?  
When a device wants to send data on an Ethernet network, it must create an **Ethernet frame**. That frame requires:
- Source MAC address (easy – it’s its own)
- Destination MAC address (this is the problem)

The device usually knows the **destination IP address**, but **not** the destination MAC address.  
**ARP solves this problem** by dynamically discovering the MAC address.

**Important**: ARP works **only within the same local network** (same broadcast domain / same VLAN). It does **not** work across routers.

### How ARP Works – Step by Step

Let’s use a simple example:  
**PC1** (IP: 192.168.1.10) wants to ping **PC2** (IP: 192.168.1.20) on the same LAN.

1. **Check ARP Cache**  
   PC1 first looks in its local **ARP cache** (a small table in RAM) to see if it already knows the MAC address for 192.168.1.20.  
   If yes → Use it and send the frame.  
   If no → Proceed to ARP request.

2. **ARP Request (Broadcast)**  
   PC1 sends an **ARP Request** packet.  
   - This is a **broadcast** frame → Destination MAC = `FF:FF:FF:FF:FF:FF`  
   - It asks: “Who has IP address 192.168.1.20? Please tell me your MAC address.”  
   - The frame contains PC1’s own IP and MAC address.

3. **All devices receive the request**  
   The switch floods the broadcast to all ports in the same VLAN.  
   Every device on the LAN receives it and checks: “Is this my IP address?”

4. **ARP Reply (Unicast)**  
   Only **PC2** (the one with IP 192.168.1.20) replies.  
   - It sends an **ARP Reply** directly (unicast) back to PC1.  
   - The reply contains: “I have 192.168.1.20 and my MAC address is 00:1A:2B:3C:4D:5E”

5. **Update ARP Cache**  
   PC1 receives the reply and adds the mapping to its ARP cache:  
   `192.168.1.20 → 00:1A:2B:3C:4D:5E`

6. **Now communication begins**  
   PC1 can now build the proper Ethernet frame with the correct destination MAC and send the ping (ICMP) packet.

### ARP Cache (ARP Table)

- A temporary table stored in RAM on every device (PCs, routers, switches).
- Entries have a **timeout** (usually 4 minutes on Windows, varies by device).
- You can view it with:
  - On Windows PC (in Packet Tracer): `arp -a`
  - To clear it: `arp -d`

### ARP Request vs ARP Reply

| Field                  | ARP Request                          | ARP Reply                              |
|------------------------|--------------------------------------|----------------------------------------|
| Destination MAC        | Broadcast (`FF:FF:FF:FF:FF:FF`)      | Unicast (sender’s MAC)                 |
| Opcode                 | 1 (Request)                          | 2 (Reply)                              |
| Target MAC             | All zeros (unknown)                  | Filled with the actual MAC             |
| Who sends it           | Device that needs the MAC            | Device that owns the requested IP      |
| Scope                  | Broadcast to entire LAN              | Direct reply to the requester          |

### Viewing ARP in Cisco Packet Tracer (Very Useful!)

1. Build a simple topology: 2 PCs + 1 Switch, same network.
2. On both PCs, open Command Prompt and type `arp -d` to clear the cache.
3. Switch to **Simulation Mode**.
4. Filter to show only **ARP** and **ICMP** packets.
5. From PC1, ping PC2.
6. You will clearly see:
   - First PDU: ARP Request (broadcast, yellow envelope)
   - Second PDU: ARP Reply (unicast)
   - Then the actual ICMP Echo Request and Reply

You can click on any PDU and inspect Layer 2 (Ethernet) and ARP details.

### Key Points to Remember

- ARP is **broadcast** for requests → consumes bandwidth (that’s why we have switches instead of hubs).
- ARP cache helps reduce repeated broadcasts.
- Routers also maintain ARP tables for each interface.
- If two devices are on **different networks**, the sender ARPs for its **default gateway** (router), not the final destination.
- Security issue: **ARP Spoofing/Poisoning** – attackers can send fake ARP replies to redirect traffic (mitigated by Dynamic ARP Inspection on Cisco switches).

### Commands Summary

| Command              | Purpose                              |
|----------------------|--------------------------------------|
| `arp -a`             | View ARP cache                       |
| `arp -d`             | Clear ARP cache                      |
| `show arp` or `show ip arp` | On Cisco routers/switches     |
