# What is ARP ?

Address Resolution Protocol, is a protocol that maps a device's IP address to its physical MAC address on a local network. It is essential for devices to communicate because while IP addresses are used for routing traffic across networks (Layer 3), a MAC address is needed to identify the specific device on a local network (Layer 2).

ARP spoofing is a cyberattack where an attacker sends false ARP messages to a local network, poisoning the Address Resolution Protocol (ARP) cache and linking the attacker's MAC address with the IP address of another device, like the default gateway. This allows the attacker to intercept, modify, or block network traffic intended for the legitimate device, enabling them to perform man-in-the-middle attacks, denial-of-service attacks, or other malicious activities

The goal of this lesson is to configure a server to protect against ARP spoofing by configuring a persistent static ARP entry for the server's default gateway that survives reboots, link resets, and restarts.

---

## Step 1: Identify Network Details

We first need to identify the machine's default gateway **IP** address and **MAC** address.
```bash
ip neigh show
```

The `ip neigh show` command shows which devices your system has recently communicated with on the local network, along with their **MAC addresses**, **interface**, and **reachability state**.

Output.
```
root@angelo:~# ip neigh show
193.191.176.254 dev eth0 lladdr ca:fe:c0:ff:ee:00 REACHABLE
```

The Output:
- Gateway IP: `193.191.176.254`
- Gateway MAC: `ca:fe:c0:ff:ee:00`
- Interface: `eth0`

---

## Step 2: Add a Static ARP Entry

First remove any dynamic ARP entry.
```bash
ip neigh del 193.191.176.254 dev eth0
```

Then add a static ARP entry.
```bash
ip neigh add 193.191.176.254 lladdr ca:fe:c0:ff:ee:00 dev eth0 nud permanent
```

Verify that the output now says permanent not reachable.
```bash
ip neigh show
```

Output.
```
root@angelo:~# ip neigh show
193.191.176.254 dev eth0 lladdr ca:fe:c0:ff:ee:00 PERMANENT
```

---

## Step 3: Make It Persistent Using Network Dispatcher

Install Network Dispatcher.
```bash
apt install networkd-dispatcher -y
```

Create and edit the dispatcher script.
```bash
nano /etc/networkd-dispatcher/routable.d/10-static-arp.sh
```

**What this does:**
- **nano** = creates file and then opens it in nano editor

File contents for `/etc/networkd-dispatcher/routable.d/10-static-arp.sh`.
```
#!/bin/bash
ip neigh replace 193.191.176.254 lladdr ca:fe:c0:ff:ee:00 dev eth0 nud permanent
```

Create and edit the helper script for dispatcher. (Optional step)
```bash
nano /usr/local/sbin/static-arp.sh
```

File contents for `/usr/local/sbin/static-arp.sh`.
```
ip neigh replace 193.191.176.254 lladdr ca:fe:c0:ff:ee:00 dev eth0 nud perman ent
```

Set the correct file permissions.
```bash
chmod 755 /usr/local/sbin/static-arp.sh
```

---

## Step 4: Configure check User for Yoda Verification

Yoda requires the check user to execute exactly these commands as root without a password.

Create and edit this file.
```bash
nano /etc/sudoers.d/check
```

File contents for `/etc/sudoers.d/check`.
```
check ALL=(ALL) NOPASSWD: /usr/sbin/ip link set dev eth0 up
check ALL=(ALL) NOPASSWD: /usr/sbin/ip link set dev eth0 down
check ALL=(ALL) NOPASSWD: /usr/lib/nagios/plugins/check_apt
check ALL=(ALL) NOPASSWD: /usr/sbin/arp
```

---

## Step 5: Verify your Configuration

Reboot your server.
```bash
sudo reboot
```

Verify that the output is still permanent not reachable.
```bash
ip neigh show
```

Now try manually shutting down and back up the port.
```bash
sudo ip link set dev eth0 down && sudo ip link set dev eth0 up &
```

Again verify that the output is still permanent not reachable.
```bash
ip neigh show
```

---
