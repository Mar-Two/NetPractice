*This project has been created as part of the 42 curriculum by mben-mer.*

# NetPractice

## Description

NetPractice is an introduction to computer networking. The goal is to solve 10 levels in a simulated network (hosts, switches, routers and the Internet) by configuring IP addresses, subnet masks and routing tables so that the devices can communicate with each other.

Each level shows a non-functioning network diagram and one or more goals (for example: *host A needs to communicate with host C*). Only the unshaded fields can be edited. A level is solved when every goal is reached, which requires understanding:

- how an IP address and a subnet mask define a network,
- how a host decides whether a destination is local or must go through a gateway,
- how routers forward packets between networks using their routing tables.

The networks are simulated in a web interface: they are not real networks.

## Instructions

### Running the training interface

1. Download the archive attached to the project page and extract it into any folder.
2. From that folder, run:

   ```sh
   ./run.sh
   ```

   This starts a local web server and opens the interface in your browser.

3. If `run.sh` does not work, start the server manually (the port number can be changed) and open `http://localhost:49242` in your browser:

   ```sh
   python3 -m http.server 49242
   ```

   A local web server is required because browsers block the pages when they are opened directly as files.

### Solving a level

1. In the **Training** tab, enter your intranet login (`mben-mer`) and click **Start!**. The login is mandatory: it generates your personal configuration, which is the one that will be checked.
2. Edit the unshaded fields (IP addresses, masks, routes).
3. Click **Check again** to verify the configuration. The logs at the bottom of the page explain why a packet is dropped (wrong gateway, no matching route, invalid IP, etc.).
4. When the level is solved, a **Next level** button appears.

The **Evaluation** tab generates random configurations, which is useful to practice for the defense.

### Exporting configurations

Before clicking **Next level**, click **Get my config** to download the configuration of the current level. Repeat this for each of the 10 levels.

### Submission requirements

- The repository must contain **10 exported configuration files (one per level), placed at the root of the repository**.
- The configurations must have been generated with the login entered in the interface.
- Double-check the file names before pushing: only the content of the repository is evaluated.
- During the defense, 3 random levels must be solved in a limited time. No external tools are allowed, except a simple calculator such as `bc`.

## Networking concepts

### TCP/IP addressing

An IPv4 address is 32 bits long, written as 4 octets (e.g. `192.168.123.129`). Combined with a subnet mask, it identifies two things: the **network** the device belongs to, and the **host** within that network.

Two devices can talk directly only if they are in the same network. Some ranges are special:

- **Private addresses** (RFC 1918): `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`. They are not routed on the Internet, so a host that must reach the Internet cannot use them as its destination-facing address.
- **Loopback**: `127.0.0.0/8` refers to the machine itself and cannot be used on a network interface.

### Subnet masks

The mask has all its bits set to 1 on the left (network part) and all its bits set to 0 on the right (host part). It can be written in decimal (`255.255.255.128`) or in CIDR notation (`/25`, the number of bits set to 1).

Worked example with `192.168.123.129` and mask `255.255.255.128` (`/25`):

- The last octet of the mask is not 255, so the block size is `256 - 128 = 128` addresses. Networks start at `.0`, `.128`.
- `129` falls in the block starting at `128`, so:

| Element            | Value                                       |
|--------------------|---------------------------------------------|
| Network address    | `192.168.123.128`                           |
| First usable host  | `192.168.123.129`                           |
| Last usable host   | `192.168.123.254`                           |
| Broadcast address  | `192.168.123.255`                           |
| Usable hosts       | `2^7 - 2 = 126`                             |

The network address and the broadcast address cannot be assigned to a device.

The same result is obtained with a bitwise AND: `IP AND mask = network address`.

### Default gateways

When a host sends a packet, it compares the destination address with its own network (using its mask):

- if the destination is in the same network, the packet is sent directly on the local network;
- otherwise, the packet is sent to the **default gateway**, which is a router interface located in the host's network. The router then forwards the packet towards the right network.

In NetPractice, a route `default` (or `0.0.0.0/0`) means "every destination", and its next hop must be reachable, i.e. in the same network as one of the device's interfaces.

### Routers and switches

- A **switch** connects several devices **within the same network**. It works at layer 2 (it forwards frames using MAC addresses) and has no IP address in NetPractice. Every device connected to the same switch must therefore be in the same subnet.
- A **router** connects **different networks** together and works at layer 3 (IP). It has one interface per network it is connected to, each with its own IP address and mask. The ranges of its interfaces must not overlap; otherwise the interfaces would be in the same network.
- A router uses its **routing table** to decide where to forward a packet: each route gives a destination network and the next hop to reach it. Both ends of a link between two routers must be in the same subnet.

### OSI layers

The OSI model splits network communication into 7 layers. NetPractice mainly involves layers 2 and 3.

| Layer | Name         | Examples                          |
|-------|--------------|-----------------------------------|
| 7     | Application  | HTTP, DNS                         |
| 6     | Presentation | Encoding, encryption (TLS)        |
| 5     | Session      | Session management                |
| 4     | Transport    | TCP, UDP (ports)                  |
| 3     | Network      | IP addresses, routers             |
| 2     | Data link    | MAC addresses, switches, frames   |
| 1     | Physical     | Cables, signals                   |

## Resources

- [Microsoft – Understanding TCP/IP addressing and subnetting basics](https://learn.microsoft.com/en-us/troubleshoot/windows-client/networking/tcpip-addressing-and-subnetting)
- [RFC 791 – Internet Protocol (IPv4)](https://www.rfc-editor.org/rfc/rfc791)
- [RFC 1918 – Address Allocation for Private Internets](https://www.rfc-editor.org/rfc/rfc1918)
- [RFC 4632 – Classless Inter-Domain Routing (CIDR)](https://www.rfc-editor.org/rfc/rfc4632)
- [Cloudflare – What is the OSI model?](https://www.cloudflare.com/learning/ddos/glossary/open-systems-interconnection-model-osi/)

Concepts studied: TCP/IP addressing, subnet masks and CIDR notation, network and broadcast addresses, private and reserved ranges, default gateways, routing tables, routers and switches, OSI layers.

### AI usage

Claude (Anthropic) was used for:

- explanations of how to compute the network address, the broadcast address and the host range from an IP address and a subnet mask;
- reviewing, restructuring and translating this README into English.

All the levels were solved manually, and every concept described here was checked against the resources listed above.





