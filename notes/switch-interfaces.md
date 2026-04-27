# CCNA Notes: Switch Interfaces

## What is a Switch Interface?

A switch interface (also called a **switch port**) is a physical or logical port on a network switch used to connect end devices, other switches, or routers. Each interface can be configured for specific roles and behaviors.

---

## Types of Switch Interfaces

### 1. Access Ports
- Belong to **a single VLAN**
- Used to connect **end devices** (PCs, printers, IP phones)
- Strips VLAN tag before forwarding to the end device
- Configuration:
```
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
```

### 2. Trunk Ports
- Carry **multiple VLANs** over a single link
- Used between **switch-to-switch** or **switch-to-router** connections
- Uses **802.1Q** tagging to identify VLANs
- Configuration:
```
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20,30
Switch(config-if)# switchport trunk native vlan 1
```

### 3. Voice Ports
- Supports both **data VLAN and voice VLAN** simultaneously
- Used with **IP phones**
```
Switch(config-if)# switchport voice vlan 100
```

---

## Interface Speed & Duplex

| Setting | Description |
|---|---|
| `auto` | Negotiates automatically (default) |
| `half` | One direction at a time |
| `full` | Both directions simultaneously |
| `10 / 100 / 1000` | Speed in Mbps |

```
Switch(config-if)# speed 100
Switch(config-if)# duplex full
```

> ⚠️ **Duplex mismatch** is a common issue — one side full, other half → collisions & poor performance.



## Interface Status

| Status | Meaning |
|---|---|
| `connected` | Link is up and operational |
| `notconnect` | No cable or device connected |
| `disabled` | Administratively shut down |
| `err-disabled` | Disabled by switch due to a violation (e.g., port security) |
Useful Show Commands

Switch# show interfaces
Switch# show interfaces fa0/1
Switch# show interfaces status
Switch# show interfaces trunk
Switch# show interfaces fa0/1 counters


 Enabling / Disabling an Interface

Switch(config)# interface fastethernet 0/1
Switch(config-if)# no shutdown     ! Enable
Switch(config-if)# shutdown        ! Disable


 Configuring a Range of Interfaces

Switch(config)# interface range fa0/1 - 24
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 10




 Interface Description

Switch(config-if)# description Link-to-PC-HR-Dept




Port Security (Brief)
- Limits the number of MAC addresses on a port
- Protects against MAC flooding attacks

Switch(config-if)# switchport port-security
Switch(config-if)# switchport port-security maximum 2
Switch(config-if)# switchport port-security violation restrict
Switch(config-if)# switchport port-security mac-address sticky


Violation modes:
| Mode | Action |
|---|---|
| `protect` | Drops unknown frames silently |
| `restrict` | Drops + sends log/SNMP alert |
| `shutdown` | Shuts port down (err-disabled) |



 Layer 3 Switch Interface (SVI)
A Switched Virtual Interface (SVI) gives a VLAN a routable IP address:

Switch(config)# interface vlan 10
Switch(config-if)# ip address 192.168.10.1 255.255.255.0
Switch(config-if)# no shutdown

# Switch Interface Errors – CCNA Notes

## Viewing Errors
```
Switch# show interfaces fastethernet 0/1
```
This command shows a counters section with all error types.

---

## Error Types Explained

### 1. 🔁 Runts
- Frames **smaller than 64 bytes**
- Usually caused by **collisions** or a faulty NIC
- Common in **half-duplex** or **duplex mismatch** situations

---

### 2. 📦 Giants
- Frames **larger than 1518 bytes**
- Caused by a **misconfigured NIC** or faulty device
- Can also occur with **jumbo frames** if not configured to support them

---

### 3. 💥 CRC Errors (Cyclic Redundancy Check)
- Frame arrived but **failed the integrity check**
- The data was **corrupted in transit**
- Common causes:
  - Bad/damaged cable
  - EMI interference
  - Duplex mismatch
  - Faulty NIC or port

---

### 4. 🔀 Input Errors
- A **umbrella counter** — includes runts, giants, CRC, frame, overrun, and ignored errors
- High input errors = **physical layer problem** (Layer 1)

---

### 5. 🧱 Frame Errors
- Frames with **incorrect framing format** — invalid length or alignment
- Often caused by **duplex mismatch** or a **bad NIC**
- Related to CRC errors but specifically about frame structure

---

### 6. 📤 Output Errors
- Frames the switch **failed to transmit**
- Can indicate **congestion**, buffer overflow, or hardware issues

---

### 7. 🚦 Collisions
- Two devices transmitted **at the same time**
- Only happen in **half-duplex** mode
- Should be **zero** on a full-duplex link
- If you see collisions on a full-duplex port → **duplex mismatch**

---

### 8. ⚡ Late Collisions
- Collision detected **after the first 64 bytes** have been sent
- In a properly functioning network, this should **never happen**
- Almost always caused by:
  - **Duplex mismatch** ← most common
  - Cable that is **too long**
  - Faulty NIC

---

### 9. 🛑 Ignored
- Frames the switch **dropped** because the input buffer was full
- Caused by **traffic bursts** overwhelming the interface

---

### 10. 🔃 Overruns / Underruns
- **Overrun** – data arrived faster than the CPU could process it
- **Underrun** – transmit buffer ran empty before the frame finished sending
- Both indicate **hardware stress or high traffic load**

---

### 11. 🚫 Deferred
- Switch **waited** before transmitting because the line was busy
- Normal in **half-duplex**, should not appear in full-duplex

---


## Clearing Counters
To reset all interface counters for a clean baseline:
```
Switch# clear counters fastethernet 0/1
```

---
