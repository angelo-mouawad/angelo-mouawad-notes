# Computer Networks

This file goes from what an IP address actually is up to routing protocols, switching, access lists and encryption. The order is deliberate, since almost nothing later makes sense until addressing and the layer model are clear.

---

## IP Addresses

An IPv4 address is a code made up of 4 numbers separated by dots. Each of those 4 numbers can range from 0 to 255, because each one is 8 bits.

That gives 32 bits in total, so `2^32` combinations, which is `4 294 967 296` addresses. It sounds like a lot until you remember how many devices exist.

There are two kinds of address you will be handed.

1. **External IP address**, the public one, the one the internet sees.
2. **Internal IP address**, the private one, the one your router hands out inside the house.

---

## Subnet Mask

An address on its own does not say where the network stops and the device starts. Internal IP addresses are split into a **network part** and a **host part**, and the subnet mask is what marks the border.

![What a subnet mask actually splits](net-ip-anatomy.svg)

There are two notations for the same thing.

```text
193.191.177.241/24          slash notation, 24 bits belong to the network
193.191.177.241  with  255.255.255.0     the same thing written as a mask
```

The mask is just the border drawn in binary. Every `1` is a network bit and every `0` is a host bit, and the ones always come first.

```text
255.255.255.0   ->   11111111.11111111.11111111.00000000
                     |----------- 24 -----------|-- 8 --|
```

Devices on the same network part talk to each other directly. Anything else has to go through a router.

---

## Private Address Ranges

A `/24` leaves 8 bits for hosts, which is 256 addresses and only 254 usable ones, since the first and the last are reserved. That is fine for a house and useless for a school.

When you need more devices you make the host part bigger, which means moving the border to the left.

| Range | Class | Mask | Roughly how many hosts |
| --- | --- | --- | --- |
| `192.168.0.0/16` | C | `255.255.0.0` | 65 thousand |
| `172.16.0.0/12` | B | `255.240.0.0` | 1 million |
| `10.0.0.0/8` | A | `255.0.0.0` | 16 million |

These three ranges are reserved for private use, so they never appear on the public internet. That is what makes them safe to reuse in every home and office at once.

A single `/24` taken out of one of them, like `192.168.1.0/24`, is what a home router actually hands out.

---

## NAT

The demand for IP addresses is clearly higher than the number available, and this is where **network address translation** comes in.

![NAT](net-nat.svg)

- NAT helps preserve the limited amount of IPv4 addresses.
- It translates private IP addresses into public ones inside the router, and back again on the way in.
- Using NAT you do not have to assign every device a public address, only every router.

Private IP addresses are assigned by your router to the devices connected to it, usually automatically. There is more detail on the three kinds of NAT further down, once routing is out of the way.

---

## DNS

When you type the name of a website into your browser, the browser has to find that site's IP address before it can connect to anything. **DNS**, the domain name system, is the service that does the lookup.

For sites you visit often the answer gets kept in your device's cache, and then no DNS server is needed at all until that cached entry expires.

The order of lookup is roughly local cache, then your router, then the DNS server your ISP or your settings point at, then the wider DNS hierarchy.

---

## Network Ports

An IP address gets you to the right machine, and a port gets you to the right program on that machine.

| Port | Protocol | What it is for |
| --- | --- | --- |
| 20 and 21 | FTP | file transfer |
| 22 | SSH | secure remote shell |
| 25 | SMTP | sending mail |
| 53 | DNS | name resolving |
| 67 and 68 | DHCP | handing out addresses |
| 80 | HTTP | websites |
| 443 | HTTPS | websites over TLS |

Ports run from `0` to `65535`. The ones below 1024 are the well known ports and normally need root to bind to.

---

## Bandwidth

Bandwidth is the capacity of a medium to carry data, and it is measured in **bits per second**.

Worth watching the unit here. Network speeds are quoted in bits, file sizes are quoted in bytes, and there are 8 bits in a byte. A 100 Mbps line downloads at roughly 12.5 MB per second at best.

---

## ISP

An **internet service provider** is a company or organisation that provides access to the internet. They act as the bridge between your router and the global network.

They are also the ones who own the public IP address you use, which is why yours can change when the modem restarts.

---

## DHCP

You can either set your device's IP address by hand or have it set automatically by a **DHCP** server, the dynamic host configuration protocol.

- **Static IP.** The user assigns the address to the device. Used for servers, printers and network gear, where the address needs to stay predictable.
- **Dynamic IP.** A DHCP server assigns the address to the device. Used for everything else, because nobody wants to configure a phone by hand.

A DHCP server hands out more than the address. It normally also supplies the subnet mask, the default gateway and the DNS servers.

---

## Configuring A Switch Or Router

To configure a switch or a router you need a console connector cable and a secure shell client such as PuTTY, which then gives you the CLI.

There are two primary command modes in the shell.

1. **User EXEC mode**, shown by `>`. Allows access to only a limited number of basic monitoring commands.
2. **Privileged EXEC mode**, shown by `#`. Allows access to all commands and features.

Getting from one to the other and into the configuration looks like this.

```text
Switch> enable
Switch# configure terminal
Switch(config)#
```

---

## Network Protocols

Network protocols define a common set of rules. Each protocol has its own function and its own format, and a device only understands another device because they agreed on both beforehand.

- **HTTP**, hypertext transfer protocol. Enables the transfer of web pages from servers to browsers. It is a request and response protocol.
- **FTP**, file transfer protocol. Enables the transfer of files between a client and a server over the internet, on a client server model.
- **SMTP**, simple mail transfer protocol. Enables sending and relaying email messages across networks. It is a push protocol.
- **DNS**, domain name system. Translates domain names into IP addresses.
- **DHCP**, dynamic host configuration protocol. Automatically assigns IP addresses to devices on a network.
- **SSH**, secure shell. Lets you securely access and manage remote servers over an unsecure network, through a secure channel where users can execute commands.
- **TCP/IP**, transmission control protocol over internet protocol. Enables reliable data transmission by breaking data into packets and making sure they arrive in order, while the IP part handles addressing and routing.

---

## Network Models

There are two models for describing the same journey. TCP/IP is what the internet actually runs on, and OSI is the teaching model with the layers broken up more finely.

![The two models side by side](net-osi-tcpip.svg)

The mapping is straightforward. The OSI application, presentation and session layers all collapse into the single TCP/IP application layer, and the OSI data link and physical layers collapse into network access.

---

## The OSI Model

Seven layers, each with a device, a component and a unit worth remembering.

- **L7 Application.** Provides the services and protocols. Unit is data.
- **L6 Presentation.** Formats the data, and encrypts it. Unit is data.
- **L5 Session.** Manages conversations and the exchange. Unit is data.
- **L4 Transport.** Breaks the data down. Unit is a segment with TCP, which is stateful, or a datagram with UDP, which is stateless.
- **L3 Network.** Logical addressing. Device is a router, component is the IP address, unit is a packet.
- **L2 Data link.** Physical addressing. Device is a switch, component is the MAC address on the NIC, unit is a frame of 64 to 1518 bytes.
- **L1 Physical.** Moves bits down cables. Device is the cabling, component is electricity or light, unit is a bit.

Each layer encapsulates the data from the layer above it, starting at L7 and ending at L1. So as data goes down the layers, more parts get added to it or wrapped around it.

---

## Data Encapsulation

This is the same idea drawn out, because it is worth seeing the headers stack up.

![What each layer wraps around the data](net-encapsulation.svg)

Going down the stack.

| Layer | What it does | What comes out |
| --- | --- | --- |
| 7 Application | the program reaches the network | data |
| 6 Presentation | makes the data usable and encrypts it | data |
| 5 Session | maintains and controls the connection | data |
| 4 Transport | transmits using TCP or UDP, adds ports | segment |
| 3 Network | decides which path the data will take | packet |
| 2 Data link | defines the format of the data on the network | frame |
| 1 Physical | transmits the raw bit stream over the cable | bits |

Layer 2 is the only one that adds something at both ends, a MAC header at the front and a trailer at the back. The receiving side does the whole thing in reverse and strips one header per layer on the way up.

---

## IPv4 Addressing

Putting the maths together on a concrete address.

![Counting the addresses in a /24](net-ipv4-subnet.svg)

```text
IP address       192.168.1.0
Subnet mask      255.255.255.0
Binary mask      11111111.11111111.11111111.00000000
Slash notation   /24, since there are 24 ones
```

The counting rules.

- **Possible addresses** = 2 to the power of the number of zeros. Here `2^8 = 256`.
- **Usable addresses** = possible addresses minus 2.
- Host bits all `0` gives the **network address**, here `192.168.1.0`, and it is not usable.
- Host bits all `1` gives the **broadcast address**, here `192.168.1.255`, and it is not usable either.
- So the range is `192.168.1.1` to `192.168.1.254`.

### Creating subnets

To split a network into subnets you borrow bits from the host part. The rule for how many to borrow is

```text
2 ^ (number of bits borrowed)  >=  number of subnets needed
```

Borrowing 3 bits gives you 8 subnets, and every borrowed bit halves the hosts you have left in each one. If you need subnets of different sizes, use **VLSM**, variable length subnet masks, which lets each subnet carry its own mask instead of forcing one size on all of them.

### IPv4 address types

- **Unicast**, one source to one destination.
- **Broadcast**, one source to all destinations on the network.
- **Multicast**, one source to a specific group of destinations.

---

## IPv6 Addressing

An IPv6 address is 128 bits instead of 32, written as eight groups of four hex digits. The first 64 bits are the prefix and the last 64 are the interface ID.

![An IPv6 address](net-ipv6-format.svg)

Two rules cut the length down.

1. **Omit leading zeros** inside a group, so `00ab` becomes `ab`.
2. **The double colon** `::` can replace one single continuous string of one or more all zero groups. Only one `::` per address, otherwise you could not tell how many groups it swallowed.

```text
2001:0db8:0000:1111:0000:0000:0000:0200      full
2001:db8:0:1111:0:0:0:200                    after rule 1
2001:db8:0:1111::200                         after rule 2
```

IPv6 was designed with subnetting in mind, so it has a separate field for it rather than borrowing host bits. The prefix takes 48 bits, the subnet field takes 16, and the interface ID keeps its full 64.

### IPv6 address types

- **Unicast**, one to one.
- **Multicast**, one to a group, starts with `FF00`.
- **Anycast**, one to the nearest member of a group.

There is no broadcast in IPv6 at all. Multicast does that job instead.

---

## GUA And LLA

Unlike IPv4 devices that get one address, an IPv6 device normally carries two at once.

![How a device gets its IPv6 addresses](net-ipv6-dynamic.svg)

### Global unicast address

A **GUA** is similar to a public IPv4 address, so a globally unique routable address. GUAs often start with `001` in binary, which shows up as `2000` in hex.

### Link local address

An **LLA** is required on every device and is used to communicate on the same local link, so the LAN. It acts like a private address and it is never routed off the link.

LLAs start with `fe80`, and they are built from the `fe80::/10` prefix with an interface ID that is either generated from **EUI-64** or picked at random. EUI-64 derives the interface ID from the MAC address of the card.

---

## Dynamic Addressing For IPv6

Devices obtain GUAs dynamically through ICMPv6.

1. **Router solicitation**, `RS`, messages are sent by host devices to discover IPv6 routers.
2. **Router advertisement**, `RA`, messages are sent by routers to tell hosts how to obtain an IPv6 GUA.

The RA can offer three methods for configuring the GUA.

- **SLAAC.** No need for DHCPv6 at all. The prefix comes from the RA messages, and the interface ID is either randomly generated or built with EUI-64.
- **SLAAC with stateless DHCPv6.** Uses SLAAC for the address itself, alongside a DHCP server that only supplies DNS and the domain name.
- **Stateful DHCPv6.** Fully relies on the DHCP server to provide the GUA as well as DNS and the domain name.

LLAs can be configured dynamically the same way, and they normally are.

---

## ICMPv6 And NDP

**ICMP** version 4 was limited to being a messaging protocol, so ping and error replies. **ICMPv6** offers additional functionality, most importantly the **neighbor discovery protocol**.

- **Neighbor solicitation**, `NS`, messages are sent to discover neighbours.
- **Neighbor advertisement**, `NA`, messages are the replies.

NDP is not only used to discover neighbours, it is also used for **duplicate address detection**, so a device can check nobody else has already claimed the address it just generated.

---

## MAC Addressing

A **MAC address** is the physical address burned into the network card, and it is 48 bits written in hexadecimal.

```text
00:1A:3F:F1:4C:C6
```

Some things worth keeping straight.

- IPv4 bits are worked with in binary, while IPv6 and MAC addresses are worked with in hexadecimal.
- Switches remember which device is on which port using a **MAC address table**.
- Routers remember which network is reachable which way using a **routing table**.

---

## The Default Gateway And ARP

The **default gateway** is the way in or out of a LAN, and it is either a router or a layer 3 switch.

When the destination IP address is on a remote network, the destination MAC address in the frame is that of the default gateway, not of the final destination. The IP address stays end to end, the MAC address changes at every hop.

Matching an address to a MAC is a protocol job.

- **ARP**, the address resolution protocol, matches a device's IPv4 address to its MAC address.
- **ICMPv6** with NDP does the same job for IPv6.

---

## Static And Dynamic Routing

- **Static routing** is configured manually, and is used for small networks that do not have to change. It is predictable and costs no CPU, but every change is your job.
- **Dynamic routing** is configured automatically with the help of protocols. It finds the best paths to transfer data on its own, and it reacts when a link goes down.

---

## Routing Concepts

A routing table contains a list of routes, and the source of each route is identified by a code in front of it.

| Code | Meaning |
| --- | --- |
| `L` | the address assigned to a router interface |
| `C` | a directly connected network |
| `S` | a static route created to reach a specific network |
| `O` | a network learned dynamically from OSPF |
| `*` | the candidate for a default route |

### The default route

The default route specifies the packet's next hop when the routing table cannot help. It is also called the **gateway of last resort**, and it is what stops a router from dropping everything it does not have an explicit entry for.

### FHRP

**First hop redundancy protocols** are mechanisms that provide alternative default gateways in switched networks where two or more routers are connected to the same VLANs.

The point is that the hosts keep pointing at one gateway address, and the routers quietly agree between themselves which of them currently answers to it. **HSRP** is one type of FHRP.

---

## TCP And UDP

The transport layer consists of two protocols, and the difference is whether anyone is keeping score.

![TCP keeps score, UDP does not](net-tcp-vs-udp.svg)

### TCP

- A **stateful** protocol which keeps track of the state of the communication session.
- Records which information it sent and which information has been acknowledged.
- Sends information and makes sure it reaches the destination, resending anything that went missing.

That reliability is why it is used for web pages, files and mail, where a missing chunk would ruin the result.

### UDP

- Does not keep track of the state of the session.
- Sends information and does not know what reaches and what does not.

That sounds worse until you think about a video call, where a retransmitted frame from two seconds ago is useless. Speed matters more than completeness, so UDP wins.

---

## Ports

A port is not a physical connection, it is a logical connection used by programs and services to exchange information.

- Ports range from `0` to `65535`.
- When data reaches the transport layer it has to be encapsulated with the TCP or UDP header, and that header has to include the source port and the destination port of the information.

The source port is picked more or less at random by your machine, and it is what lets the reply find its way back to the right browser tab rather than some other program.

---

## Virtual LANs

VLANs help you subdivide a LAN even further, for better performance and smaller broadcast domains.

![VLANs cut one LAN into several](net-vlans.svg)

- Devices on the same VLAN can talk to each other directly, even if they are plugged into different switches. That is layer 2 work.
- Devices on different VLANs need a router to communicate. That is layer 3 work.

The link between two switches that carries several VLANs at once is a **trunk**, and frames on it are tagged with `802.1Q` so the far switch knows which VLAN each frame belongs to.

### Types of VLAN

- **Data VLAN.** Carries normal user traffic like emails and web browsing. It is VLAN 1 by default, and you can create more.
- **Native VLAN.** Can be applied on a trunk link, on both ports, to allow that trunk to let untagged data through. Good for backward compatibility with devices that do not understand tagging. It is VLAN 1 by default but you can change it, and changing it is the usual security advice.
- **Default VLAN.** Is simply VLAN 1 and cannot be changed. Every port starts in it.
- **Management VLAN.** Used for secure administrative access to the switch. It is often assigned to the **switched virtual interface**, the SVI, which is what gives the switch an IP address of its own.

---

## Inter-VLAN Routing

Three ways to get traffic from one VLAN to another, in the order they were invented.

![Three ways to route between VLANs](net-inter-vlan.svg)

### Legacy routing

The first inter VLAN routing solution relies on using a router with two interfaces connected to two different VLANs. Very limited, since you need one physical router interface per VLAN.

The router interfaces served as the default gateways for the local hosts on each VLAN subnet.

### Router on a stick

Uses only one interface connected to all the VLANs through a trunk port.

The router interface is configured using virtual **subinterfaces**, each with its own IP address and its own VLAN assignment. Packets are `802.1Q` tagged so the router can tell which VLAN each one came from. Fine up to a few dozen VLANs, and after that the single link becomes the bottleneck.

### Layer 3 switch

Uses a **switched virtual interface**. An SVI is a virtual interface on the layer 3 switch that divides the switch into multiple VLANs, and each SVI acts as the gateway for its VLAN.

No router in the path at all, which makes this the most scalable and the modern answer.

---

## STP Concepts

A loop in an ethernet LAN can cause problems and infinite transmission of ethernet frames, because a switch has no equivalent of the TTL field that saves routers from the same fate.

The **spanning tree protocol** is a loop prevention protocol that still allows redundancy. It works by blocking a specific port in the loop, and if failures happen on the working ports, STP knows it should automatically reopen the port it blocked.

![STP picks one port to block](net-stp.svg)

### Steps to block the loop

Using the **spanning tree algorithm**, STP blocks the loop in four steps.

1. Elect the root bridge, so the main switch.
2. Elect the root ports.
3. Elect the designated ports.
4. Elect the alternate or blocked ports.

During STA and STP, switches send out **bridge protocol data units**, the BPDUs, which contain **bridge IDs**. A BID contains a priority value, the MAC address of the switch, and an extended system ID.

### Step 1, elect a root bridge

The root bridge is the switch that acts as the reference point for all path calculations.

- Every switch, after it boots, starts sending BPDUs containing its BID.
- The switch with the **lowest BID** becomes the root bridge.
- After electing a root bridge, the STA starts determining the best paths back to it from every other destination. This is done using internal root path costs, so the shortest and fastest path through the ports is selected.

### Step 2, elect the root ports

Every non root switch selects the port closest to the root bridge as its **root port**. One per switch, and only non root switches have one.

### Step 3, elect the designated ports

- All ports on the root bridge are designated ports.
- If one end of a segment is a root port, the other end is a designated port.
- All ports attached to end devices are designated ports.
- On segments between two non root switches, the port on the switch with the lower BID is the designated port.

### Step 4, elect the alternate or blocked ports

If a port is not a root port and not a designated port, it is an **alternate port**, and it gets blocked to prevent the loop.

### Remarks

When a switch has multiple equal cost paths to the root bridge, it determines its root port using, in order,

1. lowest BID,
2. lowest port priority,
3. lowest port id.

Different VLANs will have their own STP instances and their own root bridge, which means you can deliberately balance traffic by making different switches the root for different VLANs.

---

## EtherChannel

EtherChannel bundles up multiple physical links between two devices into one logical link, to increase bandwidth and provide redundancy.

![EtherChannel bundles the links](net-etherchannel.svg)

It is like saying that instead of one lane, let us group four ethernet lanes into a fat highway, but to the network it still looks like one logical interface.

That last part is the important bit. Because STP sees a single link, it does not block any of the members, which is exactly the problem you would otherwise have with four parallel cables.

**LACP** helps create the EtherChannel link by detecting the configuration of each side and making sure that they are compatible. If the two ends disagree on speed, duplex or VLANs, the bundle does not come up.

---

## OSPF Concepts

The protocol stands for **open shortest path first**, and it is a link state protocol.

- OSPF is used to find the best path for IP traffic.
- It forms neighbour relationships and builds a map of the network.
- That map is called the **LSDB**, the link state database.

### Single area OSPF messaging

Routers running OSPF exchange routing information using 5 types of packet, in this order.

1. **Hello** packet.
2. **Database description** packet, `DBD`.
3. **Link state request** packet, `LSR`.
4. **Link state update** packet, `LSU`.
5. **Link state acknowledgment** packet, `LSAck`.

These packets are used to discover neighbouring routers and also to exchange routing information, so that everyone keeps accurate information about the network.

Worth separating two names that look alike. An `LSA` is a link state advertisement, the actual piece of information about a link, and it travels inside an LSU. The `LSAck` is the acknowledgement that one arrived.

### Single area OSPF databases

- **Neighbour table**, the adjacency database.
- **Topology table**, the link state database, the LSDB.
- **Routing table**, the forwarding database.

---

## OSPF States

Two routers walk through these states before they are fully adjacent.

![The OSPF neighbour states in order](net-ospf-states.svg)

- **Down state.** No Hello packets received, so the router starts sending its own. Transitions to Init.
- **Init state.** Hello packets received from a sender, and they contain the router id of that sender. Transitions to Two way.
- **Two way state.** Communication becomes bidirectional. On multiaccess links the routers elect a DR and a BDR here. Transitions to Ex start.
- **Ex start state.** On single access links, the two routers decide who will send the DBD packet first.
- **Exchange state.** Routers exchange DBD packets. If more information is needed it transitions to Loading, otherwise straight to Full.
- **Loading state.** Gain the missing information with LSR and LSU packets. Transitions to Full.
- **Full state.** The link state database of the router is fully synchronised.

**SPF** is the algorithm used to choose the best routes between two routers, and those routes are then added to the routing tables.

---

## OSPF Router ID

A router id is a 32 bit number formatted like an IPv4 address, and every router needs one to participate in OSPF.

The id can be defined manually or automatically, and it is used for two things.

- It participates in the synchronization of databases. During the exchange state, the router with the highest id sends DBD packets first.
- It participates in the election of the designated router. On a multiaccess LAN the router with the highest router id is the DR, and the second highest is the BDR.

### How the id is chosen

Routers derive their id based on one of three criteria, in order.

1. Using the `router-id` command.
2. Using the highest IPv4 address of any configured loopback interface.
3. Using the highest IPv4 address of any of its physical interfaces.

A loopback is the usual choice in practice, because a loopback interface never goes down and so the id never changes underneath you.

---

## OSPF Designated Router

In multiaccess networks, OSPF elects a DR and a BDR so that every router does not have to form an adjacency with every other router.

![DR and BDR on a multiaccess network](net-ospf-dr.svg)

- The DR is responsible for collecting and distributing the LSA packets sent and received.
- All other routers are **DROthers**. They send their updates to the multicast address `224.0.0.6`, which only the DR and the BDR listen on.
- The DR then passes information back out on `224.0.0.5`, which every OSPF router listens on.

### The election

- The DR is the router with the highest **priority**, a value from 0 to 255 which can be configured. The BDR is the router with the second highest priority.
- If priorities are equal then the router ids are checked instead.
- Adding a new router does not trigger a new election. The existing DR stays DR until it fails, which stops the network reshuffling every time someone plugs in a switch.

A priority of `0` means the router will never become DR or BDR at all.

### OSPF cost metric

This metric is used to determine the best path across a network, and it is also used by STP.

```text
cost = reference bandwidth / interface bandwidth
```

Lower cost indicates a better path, so higher bandwidth gives lower cost.

---

## Switch Security

Ports are the way into a network, so the defaults are worth changing.

### Port security

By limiting the number of permitted MAC addresses on a port to one, port security can be used to control unauthorized access to the network. If a second MAC shows up, the port reacts, usually by shutting itself down.

### Common attacks and what stops them

- **VLAN attacks.** Disable the dynamic trunking protocol, `DTP`, and set unused ports to access mode, to prevent unauthorized VLAN trunking.
- **DHCP attacks.** Enable **DHCP snooping** to block rogue DHCP servers and validate DHCP messages.
- **ARP attacks.** Use **dynamic ARP inspection**, `DAI`, to verify ARP replies against trusted sources.
- **STP attacks.** Enable **BPDU guard** on access ports to prevent rogue switches from influencing STP and making themselves the root bridge.

The theme across all four is that an access port should never be trusted to do infrastructure work.

---

## ACL Concepts

An **ACL** is a series of IOS commands that are used to filter packets based on information found in the packet header.

An ACL uses a sequential list of permit or deny statements, known as **access control entries**, the ACEs.

When network traffic passes through an interface configured with an ACL, the router compares the information in the packet against each ACE in sequential order. This is called **packet filtering**, and it happens at layer 3 and sometimes layer 4.

### Two types of ACL

- **Standard ACL**, layer 3. Uses the source IPv4 address to filter.
- **Extended ACL**, layer 3. Uses the source and destination IPv4 addresses to filter.
- **Extended ACL**, layer 4. Uses TCP and UDP ports to filter as well.

### Where to put them

![Where to put each kind of ACL](net-acl-placement.svg)

- **Extended ACLs** should be located as close to the source of the traffic as possible, so unwanted traffic dies before it crosses the network.
- **Standard ACLs** should be located as close to the destination as possible, because they only see the source address and would otherwise block traffic heading somewhere legitimate too.

Every ACL ends with an invisible deny all, so an ACL with only permit statements still blocks everything you forgot to mention.

### VTY virtual terminal

By default any device on the network can SSH or Telnet into your router, and that is a security risk.

To limit access to only trusted IPs, you create a standard ACL and apply it to the **virtual terminal lines**, the VTY lines, rather than to a physical interface.

---

## NAT For IPv4

Coming back to NAT now that routing is in place.

NAT provides the translation of private addresses to public addresses, and the router holds a **NAT table** to remember the translations it has made.

![The NAT table and the three flavours](net-nat-types.svg)

### The NAT table

- **Inside local**, the device's own private IP.
- **Inside global**, the router's public IP as the outside world sees it.
- **Outside global**, the destination's real public IP.
- **Outside local**, how the destination appears from inside the network, which is usually the same address.

### Three types of NAT

- **Static NAT.** Uses a one to one mapping of addresses, configured manually, and it remains constant. Used when a server inside has to be reachable from outside at a fixed address.
- **Dynamic NAT.** Uses a pool of public addresses and assigns them on a first come first served basis. When the pool runs out, the next device waits.
- **PAT.** Maps multiple private addresses to a single public address, or sometimes to a couple of public addresses. PAT uses the source port number to uniquely identify each specific NAT translation.

PAT is what your home router does, which is exactly why the whole house can share one address.

---

## Information Confidentiality And Security

Three properties are what security is trying to protect, and they are usually taught together.

- **Confidentiality**, the information should only be readable by whoever is meant to read it.
- **Integrity**, the information should be consistent and unchanged in transit.
- **Availability**, the information should actually be there when it is needed.

Encryption addresses the first two. The third one is a network design problem, which is where redundancy, FHRP and EtherChannel come back in.

---

## The Three Types Of Encryption

- **Symmetric encryption**, secret key.
- **Asymmetric encryption**, public key.
- **Hashing**, one way only.

![Same key on both sides, or a pair of keys](net-symmetric-asymmetric.svg)

---

## Symmetric Encryption

The key for encryption and the key for decryption are the same one.

### Block cipher encryption

The data is cut into fixed size blocks and each block is encrypted.

1. **DES**, data encryption standard. An older block cipher that encrypts 64 bit blocks with a key of 56 bits, and it is considered insecure.
2. **3DES**, triple DES. An extension of DES that encrypts the data 3 times, also insecure now.
3. **AES**, advanced encryption standard. The newest and most commonly used block cipher, which encrypts blocks of 128 bits with keys of 128, 192 or 256 bits.

### Stream cipher encryption

Encrypts the data one bit or byte at a time rather than in blocks. RC4, ChaCha20 and Grain 128 are examples, and they are used for less capable devices. RC4 is insecure and should not be picked today, though ChaCha20 is still widely trusted.

### Block cipher encryption modes

- **ECB mode.** Uses the same key with no extra input, so the same plaintext block will always give the same ciphertext block, which leads to visible patterns in the output.
- **CBC mode.** Uses a key plus randomness carried over from the previous block, so patterns will not emerge. This is the block chaining part.

The classic demonstration is encrypting an image with ECB. You can still make out the picture, because identical regions encrypt identically.

---

## Asymmetric Encryption

The key used for encryption, the **public key**, and the key used for decryption, the **private key**, are different.

You hand the public key to anybody who wants it and you keep the private key to yourself. Anything locked with the public key can only be opened with the matching private key.

### Types

- **RSA.** Older, long key, slower. Used for servers and older systems.
- **ECC.** Newer, short key, faster. Used for mobile devices and digital signatures.

### Why it exists

Symmetric encryption needs its own key for each relationship, which means each user ends up holding many keys, and that is not practical. With `n` people you need `n(n-1)/2` keys in total.

Asymmetric encryption gives each user one public key they give out and one private key they keep, so each user only has 2 keys no matter how many people they talk to.

---

## Hashing

Hashing only encrypts information, it cannot be decrypted, and it returns a hash value or hash code of a fixed length.

![Hashing only goes one way](net-hashing.svg)

### Types of hash function

- **SHA-0 and SHA-1**, no longer safe, since collisions have been produced in practice.
- **SHA-2**, currently in use, and `SHA-256` is the common member of that family.
- **SHA-3**, standardised and available, though SHA-2 is still the default almost everywhere.

### Applications of hashing

- Password storage.
- Digital signatures.
- Blockchain technology.
- Search engines and data indexing.
- Cryptographic hashing inside encryption schemes.

For passwords the hash is deliberately slowed down and salted, using something like bcrypt or Argon2, so that guessing millions of candidates stops being cheap.

---

## Hybrid Encryption

Hybrid encryption is a combination of asymmetric encryption, symmetric encryption and hashing, with the goal of maximising the strengths of each method.

In practice that means asymmetric encryption is used once at the start, only to agree on a shared symmetric key. After that the actual data is encrypted symmetrically because it is far faster, and hashing is used alongside to prove nothing was tampered with.

This is what HTTPS does on every page you load.

---

## Certificates

A public key on its own proves nothing, since anybody can generate one and claim it belongs to a bank. A **certificate** is a public key with an identity attached and a signature from somebody you already trust.

![Self signed against signed by a CA](net-certificates.svg)

- A **self signed certificate** is signed with its own private key, so the issuer and the subject are the same. Verification fails against any trust store, which is fine for a lab and useless in public.
- A **CA signed certificate** is signed by a **certificate authority**. The issuer is the CA and the subject is the server, so they differ, and verification against the CA certificate succeeds.

The commands for creating both of these live in the Linux Bash file, in the openssl sections.

---

## Quick Recap

- The mask says where the network stops, `/24` means 24 network bits and 254 usable hosts.
- NAT exists because IPv4 ran out, and PAT is the version your router runs.
- OSI has 7 layers, TCP/IP has 4, and each layer down adds a header.
- Router is layer 3 and works with IP, switch is layer 2 and works with MAC.
- TCP acknowledges everything, UDP acknowledges nothing.
- VLANs split a LAN, trunks carry several VLANs, and a router or SVI is needed to cross between them.
- STP blocks exactly one port per loop and reopens it when a link fails.
- OSPF builds a map, elects a DR on multiaccess links, and picks paths by cost.
- Standard ACLs go near the destination, extended ACLs go near the source.
- Symmetric is fast, asymmetric solves key distribution, hashing goes one way, and hybrid uses all three.
