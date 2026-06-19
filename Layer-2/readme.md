# Layer 2

<img src="./images/image1.png" width="1000px" />

- It sits just above the physical layer and does two big jobs: getting data reliably from one device to the next device on the **same network**, and controlling who gets to use the shared wire at any given moment.

## What Layer 2 does

It takes raw bits from Layer 1 and gives them meaning — packaging them into frames with a clear source, destination, and error check. Where Layer 3 (IP) handles routing across the whole internet, `Layer 2 only cares about the next hop — the immediate neighbour on the same` network.

## MAC Addresses

Every network interface has a burned-in 48-bit MAC address (like AA:BB:CC:DD:EE:FF). The first 3 bytes identify the manufacturer (the OUI), the last 3 are a unique device serial. Unlike IP addresses, MACs don't change when you move networks — they're tied to the hardware.

## The switch (the key Layer 2 device)

A switch learns which device is on which port by watching incoming frames. When PC-A sends a frame, the switch reads the source MAC, records "AA:AA:... is on port 1", then looks up the destination MAC to decide where to send it — not flooding everyone, just the right port. If it doesn't know the destination yet, it does flood (sends to all ports) and learns from the reply. Ports means in here the physical connections on the switch, not IP ports.

## Error detection with FCS

The Frame Check Sequence at the end of every Ethernet frame is a CRC checksum. The receiver recalculates it — if it doesn't match, the frame is silently dropped. Layer 2 detects errors but doesn't correct them; that's left to higher layers.

## Key protocols at Layer 2

Ethernet (wired), Wi-Fi / 802.11 (wireless), VLANs (802.1Q), STP (Spanning Tree Protocol to prevent loops), and ARP (which bridges Layer 2 and 3 by mapping IP addresses to MACs).

<img src="./images/image2.png" width="1000px" />

- Carrier Sense — before transmitting, a device listens to the wire. If it hears activity (a "carrier"), it waits. This alone cuts collisions dramatically.
- Multiple Access — acknowledges that many devices share the same medium. There's no central controller; everyone follows the same rules.
- Collision Detection — while transmitting, a device keeps listening. If it hears a signal that isn't its own, it knows a collision has happened. It immediately stops sending and broadcasts a 32-bit jam signal to make sure every device on the network hears about the collision.
- Random backoff (BEB) — after a collision, both devices wait a random amount of time before retrying. This randomness is what actually resolves the deadlock — if they both waited the same fixed time, they'd collide again forever. The algorithm is called Binary Exponential Backoff: after the 1st collision, pick a random wait from {0, 1} slots; after the 2nd collision, from {0, 1, 2, 3} slots; after the 3rd, from {0..7} — the window doubles each time. After 16 failed attempts the frame is dropped.

<img src="./images/image3.png" width="1000px" />

### Why modern switches mostly eliminated this problem

CSMA/CD is the answer for a shared bus or a hub. But a modern Ethernet switch gives every device its own dedicated full-duplex link — there's no shared wire anymore. Each port gets its own private collision domain, so two devices can transmit and receive simultaneously without ever conflicting. CSMA/CD still exists in the standard, but in practice it almost never fires on a switched network.

<img src="./images/image4.png" width="1000px" />

#### Hub

one big collision domain. A hub is a dumb repeater: whatever arrives on any port gets blasted out every other port. All four devices share the same wire electrically. If A and C both transmit at the same moment, they collide. Everyone on the hub has to take turns using CSMA/CD.

#### Switch

one collision domain per port. A switch doesn't repeat blindly — it buffers the frame, reads the destination MAC, and forwards it only to the right port. The link between PC-E and the switch is private to just those two. PC-E and PC-G can transmit simultaneously with zero chance of collision because they're on separate segments. Each port is its own isolated collision domain.
A useful rule of thumb:

- Every hub port added to a network → stays in the same collision domain
- Every switch port → creates a new, isolated collision domain

### But on the PC side, ARP is what fills in that destination MAC before the frame is even created. The switch only handles forwarding after the frame arrives.

### 🛑 Layer 2 frame header gets dropped and recreated at every hop, while the Layer 3 packet header stays mostly intact.

---

## How Layer 2 Solves Layer 1's Limitations

### Problem 1 — No Intelligence / No Addressing

**Layer 1:** Hub sends every bit out of every port blindly.

**Layer 2 fix → MAC Addressing**

Every device gets a unique **48-bit MAC address** burned into its NIC. Layer 2 wraps bits into **frames** that contain:

```
| Destination MAC | Source MAC | Type | Payload | FCS |
```

A **switch** (Layer 2 device) reads the destination MAC and forwards the frame **only to the correct port** — not everyone. It builds a **MAC address table** by learning which MAC lives on which port over time.

```
Switch MAC Table:
Port 1 → AA:BB:CC:11:22:33  (PC A)
Port 2 → AA:BB:CC:44:55:66  (PC B)
Port 3 → AA:BB:CC:77:88:99  (PC C)
```

So instead of shouting at everyone, Layer 2 **delivers to exactly the right device**.

### Problem 2 — No Error Detection

**Layer 1:** Corrupt bits go completely unnoticed.

**Layer 2 fix → FCS / CRC (Frame Check Sequence)**

When a frame is created, Layer 2 runs a **CRC (Cyclic Redundancy Check)** calculation on the data and appends the result as an **FCS field** at the end of the frame:

```
Sender:   Data → CRC calculation → appends FCS value
Receiver: recalculates CRC on arrival → compares with FCS
          ✓ Match   → frame accepted
          ✗ Mismatch → frame DROPPED (corruption detected)
```

Layer 2 doesn't _fix_ the error — it detects and **discards** corrupted frames. Retransmission is then handled by Layer 4 (TCP).

### Problem 3 — Collisions

**Layer 1:** Two devices transmit at the same time → signals collide → data destroyed.

**Layer 2 fix → CSMA/CD + Switches**

Layer 2 introduced **CSMA/CD** (Carrier Sense Multiple Access with Collision Detection) for shared media:

```
1. CARRIER SENSE   → listen before transmitting
                     "Is anyone else talking?"
2. MULTIPLE ACCESS → all devices share the medium
3. COLLISION DETECT → if collision occurs, stop immediately
                      send a JAM signal to alert all devices
                      wait a random backoff time, then retry
```

But the **real solution** was replacing hubs with **switches**:

|              | Hub (Layer 1)            | Switch (Layer 2)                     |
| ------------ | ------------------------ | ------------------------------------ |
| Domain       | One big collision domain | Each port = its own collision domain |
| Transmission | Half-duplex              | Full-duplex                          |
| Collisions   | Constant                 | Eliminated                           |
| Intelligence | None                     | MAC table, frame forwarding          |

With a switch, PC A and PC B can transmit **simultaneously** without collision because each port is its own dedicated segment.

### Big Picture

```
Layer 1 Problem          Layer 2 Solution
─────────────────────────────────────────────
No addressing       →    MAC addresses + frames
No error detection  →    CRC / FCS check
Collisions          →    CSMA/CD + switches
```

Layer 2 essentially gave the network **identity, integrity, and order** — the three things Layer 1 completely lacked.

---

## Limitations of Layer 2 — Data Link Layer

Layer 2 solved Layer 1's problems but introduced its own limitations:

### 1. No Inter-Network Communication

Layer 2 only works **within the same network (LAN)**. MAC addresses have no concept of networks — they are flat, local identifiers. A switch cannot forward a frame to a device on a **different network**. For that you need a router (Layer 3).

```
PC A (192.168.1.10) ──── Switch ──── PC B (192.168.1.20)  ✓ works
PC A (192.168.1.10) ──── Switch ──── PC C (10.0.0.5)      ✗ can't reach
```

### 2. No Logical Addressing

MAC addresses are **hardware-burned** and have no logical meaning. You cannot tell from a MAC address:

- Which country/city a device is in
- Which organization it belongs to
- Which network segment it's on

IP addresses (Layer 3) solve this by being **hierarchical and routable**.
