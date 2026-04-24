## Routing Fundamentals & Route Selection



### What is Routing?

Routing is the process of determining the best path for packets to travel from a source to a destination across one or more networks. Routers operate at **Layer 3 (Network Layer)** and make independent, local forwarding decisions at each hop — no single router knows the full end-to-end path.



### How a Router Processes a Packet

When a packet arrives at a router:

1. Strip the Layer 2 frame, extract the IP packet
2. Read the **destination IP address**
3. Consult the **routing table** for a match
4. Apply **route selection logic** to pick the best route
5. Forward the packet out the correct interface toward the next hop
6. Decrement TTL; discard if TTL = 0



### The Routing Table

Every router maintains a routing table — a database of known destinations and how to reach them. Each entry contains:

| Field | Description |
|---|---|
| **Destination Prefix** | Network address + mask (e.g., `10.0.0.0/8`) |
| **Next Hop** | IP of the next router, or `directly connected` |
| **Outgoing Interface** | Physical/logical interface to use |
| **Metric** | Protocol-specific cost (hops, bandwidth, delay, etc.) |
| **Administrative Distance** | Trustworthiness of the source protocol |
| **Route Source** | How it was learned (C, S, O, B, R, etc.) |



### How Routes Are Learned

**Directly Connected (C)**
Automatically installed when an interface is up and configured. The router knows its own subnets with AD = 0.

**Static Routes (S)**
Manually configured by an admin. Predictable and low-overhead but don't adapt to failures. AD = 1. Commonly used for default routes.

**Dynamic Routing Protocols**
Routers exchange reachability information and adapt automatically:

*IGPs — within an Autonomous System:*
- **RIP** — distance vector; hop count metric; max 15 hops; slow convergence; AD = 120
- **OSPF** — link-state; cost based on bandwidth; Dijkstra's SPF algorithm; fast convergence; AD = 110
- **EIGRP** — hybrid (Cisco); composite metric (bandwidth + delay); AD = 90 internal

*EGP — between Autonomous Systems:*
- **BGP** — path vector; internet's inter-domain protocol; policy-driven; AD = 20 (eBGP) / 200 (iBGP)



### Route Selection — The Decision Process

When a router has multiple possible routes to the same destination (learned via different protocols or paths), it must choose the best one. The selection follows a strict **3-tier hierarchy**:



#### Tier 1 — Longest Prefix Match (LPM)

This is always applied **first**, before anything else. The route with the most specific (longest) subnet mask wins, regardless of protocol or metric.

```
Routing Table:
  10.0.0.0/8      → Next Hop A
  10.10.0.0/16    → Next Hop B
  10.10.10.0/24   → Next Hop C

Destination: 10.10.10.5

  Matches /8  ✓
  Matches /16 ✓
  Matches /24 ✓  ← WINNER (most specific)


This is the single most important rule in routing. A `/32` host route always beats everything else for that specific IP.



#### Tier 2 — Administrative Distance (AD)

If two routes match with the **same prefix length** but come from **different protocols**, AD decides. AD represents how trustworthy the source of the route is. **Lower AD wins.**



**Example:** If OSPF advertises `192.168.1.0/24` (AD 110) and RIP also advertises `192.168.1.0/24` (AD 120), only the OSPF route is installed in the routing table.

AD is sometimes called the **"believability"** of a route — it doesn't measure path quality, just source trustworthiness.



#### Tier 3 — Metric

If two routes have the **same prefix** and come from the **same protocol**, the metric decides. Each protocol defines its own metric:


**Example (OSPF):** Two OSPF paths to `172.16.0.0/24` — one via a 1 Gbps link (cost 1), one via a 100 Mbps link (cost 10). The 1 Gbps path wins.

If metrics are **equal**, most protocols support **ECMP (Equal Cost Multi-Path)** — load balancing traffic across all equal-cost paths simultaneously.



### Full Route Selection Summary


New packet arrives — destination IP known
         │
         ▼
┌─────────────────────────────┐
│  1. Longest Prefix Match    │  ← Most specific route wins
│     /24 beats /16 beats /8  │
└────────────┬────────────────┘
             │ Tie (same prefix length)
             ▼
┌─────────────────────────────┐
│  2. Administrative Distance │  ← Most trusted protocol wins
│     Lower AD = preferred    │
└────────────┬────────────────┘
             │ Tie (same protocol)
             ▼
┌─────────────────────────────┐
│  3. Metric                  │  ← Best path cost wins
│     Lower metric = preferred│
└────────────┬────────────────┘
             │ Tie (equal metric)
             ▼
        ECMP / Load Balance
        across equal paths


### What Happens When No Route Matches?

- If a **default route** (`0.0.0.0/0`) exists → packet is forwarded there (gateway of last resort)
- If **no default route** → packet is dropped and an **ICMP Destination Unreachable** message is sent back to the source



### Convergence

After a topology change (link failure, new route), routers must update their tables and reach a consistent, loop-free view — this is **convergence**. Key considerations:

- **OSPF/IS-IS** converge in seconds (link-state flooding + SPF recalculation)
- **RIP** can take minutes (periodic updates, counting to infinity problem)
- **BGP** convergence can take minutes to hours across the internet due to policy complexity
- During convergence, **transient loops and black holes** can occur



### Key Takeaway

Route selection is a deterministic, hierarchical process: **specificity first (LPM), then trust (AD), then cost (metric)**. Understanding this hierarchy is essential for diagnosing routing issues, designing networks, and predicting how traffic will flow under normal and failure conditions.


## Connected and Local Routes

These are the two most fundamental route types — automatically created by the router itself, requiring no configuration beyond assigning an IP address to an interface.



### Connected Routes

A **connected route** represents the entire network (subnet) that an interface belongs to. It is automatically added to the routing table when:

- An interface is assigned an IP address, **and**
- The interface is in an **up/up** state (line protocol up)

**Example:**
If you configure a router interface with `192.168.10.1/24`, the router installs:

C    192.168.10.0/24 is directly connected, GigabitEthernet0/0


This tells the router: *"any destination within `192.168.10.0/24` is reachable out of Gi0/0 — no next hop needed, I am directly on that network."*

**Key properties:**
- Administrative Distance = **0** — most trusted route type after local
- No next hop IP — the router itself is on that network
- Removed automatically if the interface goes down
- Represents reachability for the **entire subnet**



### Local Routes

A **local route** is a `/32` host route representing the **router's own IP address** on an interface. It was introduced in IOS 12.x and is always present alongside a connected route.

Using the same example above, the router also installs:


L    192.168.10.1/32 is directly connected, GigabitEthernet0/0


This tells the router: *"this specific IP address belongs to me — packets destined here are for my own processes, not to be forwarded."*

**Key properties:**
- Administrative Distance = **0**
- Always a **/32** (single host) regardless of the actual interface mask
- Used to efficiently identify traffic **destined for the router itself**
- One local route per interface IP address



### Connected vs. Local — Side by Side

| | Connected Route | Local Route |
|---|---|---|
| **Prefix** | Subnet (e.g., `/24`) | `/32` host route |
| **Represents** | The whole network on the interface | The router's own IP |
| **Purpose** | Forward traffic to hosts on that subnet | Receive traffic addressed to the router |
| **AD** | 0 | 0 |
| **Next Hop** | Directly connected (no hop) | Directly connected (no hop) |
| **Created when** | Interface is up/up with an IP | Same — always paired with connected |



### How They Work Together — Practical Example

Suppose a router has two interfaces configured:


GigabitEthernet0/0 → 192.168.10.1/24
GigabitEthernet0/1 → 10.0.0.1/30

The routing table will automatically contain:


C    192.168.10.0/24   is directly connected,  Gi0/0
L    192.168.10.1/32   is directly connected,  Gi0/0

C    10.0.0.0/30       is directly connected,  Gi0/1
L    10.0.0.1/32       is directly connected,  Gi0/1


Now consider three incoming packets:

**Packet 1 — destined for `192.168.10.50`**
Matches the connected route `192.168.10.0/24` → router forwards it out Gi0/0 (LPM: /24 beats /32 for this address since `192.168.10.50 ≠ .1`)

**Packet 2 — destined for `192.168.10.1`**
Matches both `192.168.10.0/24` (C) and `192.168.10.1/32` (L). LPM picks `/32` → router recognizes this as **its own address** and hands it to its own IP stack (e.g., SSH, OSPF, ping response)

**Packet 3 — destined for `10.0.0.1`**
Matches `10.0.0.1/32` (L) → again, router's own address, processed locally



### Why the Local Route Matters

Without the local `/32` route, the router would have to inspect every packet more carefully to determine if it is the intended destination. The local route makes this **efficient and unambiguous** via normal LPM lookup — the same mechanism used for all forwarding decisions.

It also matters in scenarios like:
- **OSPF/BGP peering** — the router must receive packets to its own IP; the local route ensures they aren't forwarded onward
- **Management access** — SSH/Telnet to the router's interface IP hits the local route
- **Ping/traceroute responses** — ICMP echo requests to the router are caught by the local route



### Key Takeaway


Interface configured + up/up
         │
         ├──→  Connected /24  — "I can reach this whole subnet directly"
         │
         └──→  Local /32      — "This specific IP is mine; process it locally"


Every working interface gives you both. They are the foundation on which all other routing (static, OSPF, BGP) builds — you cannot forward or receive anything without them.