# What Is IPV6 ?

**IPv6 which stands for the Internet Protocol version 6** is the newest version of the Internet Protocol, designed to replace IPv4. It provides a **much larger address space**, better performance, improved security features, and modernizes how devices communicate on the internet.

The goal of this this lesson is to:
- **Learn how to configure IPv6 manually** using `systemd-networkd` with static address, gateway, and routes.
- **Understand dual-stack networking** by running IPv4 and IPv6 together on the same server.
- **Ensure services and their ports listen on IPv6**, and verify them using tools like `ss` and `ip`.
- **Add and test IPv6 DNS records (AAAA)** so your domain resolves correctly over IPv6.

After this we can get an IPV6 Certification. An IPV6 Certification is verifying, and proving that your server and services fully support IPv6.

 You need to understand how this differs from HTTPS certificates because they are **completely different concepts**.
- **HTTPS certificate**: Proves website identity & encrypts traffic
- **IPv6 certification**: Proves IPv6 connectivity & correctness

---

## Step 1: Setup

Create `.pve-ignore` so Proxmox won't overwrite your network file at boot.
```Bash
touch /etc/systemd/network/.pve-ignore.eth0.network
```

Write the `systemd-networkd` file for a static IPv4 and IPv6 dual stack configuration. Create and edit this file.
```bash
nano /etc/systemd/network/eth0.network
```

**What this does:**
- **nano** = creates file and then opens it in nano editor

File contents for `/etc/systemd/network/eth0.network`.
```
[Match]
Name=eth0

[Network]
# Should already be there for ipv4
Description=Interface eth0 autoconfigured by PVE
Address=193.191.176.212/24
Gateway=193.191.176.254
DHCP=no
IPv6AcceptRA=no

# Add these new lines for ipv6
Address=2001:6a8:2880:a020::d4/64
Gateway=2001:6a8:2880:a020::fe
```

Restart `systemd-networkd` to apply changes.
```bash
systemctl restart systemd-networkd
```

Verify the ipv6 address.
```
ip -6 addr show dev eth0
ip -6 route show
```

**What this does:**
- **ip** = shows and manages IP addresses

The output should include.
- Your ipv6: `inet6 2001:6a8:2880:a020::d4/64 scope global`
- And the route to `::/0` via `2001:6a8:2880:a020::fe`

---

## Step 2: Port control

Ensure the IPv6 chains `ip6tables` allow these ports. We want to make sure that all our previous labs also work with ipv6.
```bash
ip6tables -A INPUT -p tcp --dport 25 -j ACCEPT
ip6tables -A INPUT -p tcp --dport 53 -j ACCEPT
ip6tables -A INPUT -p tcp --dport 80 -j ACCEPT
ip6tables -A INPUT -p tcp --dport 443 -j ACCEPT
ip6tables -A INPUT -p tcp --dport 143 -j ACCEPT
ip6tables -A INPUT -p tcp --dport 993 -j ACCEPT
ip6tables -A INPUT -p tcp --dport 22345 -j ACCEPT
```

Port details:
- 25 = used for SMTP by Postfix
- 53 = used for DNS by Bind 
- 80 = used for HTTP by Apache2
- 443 = used for HTTPS by Apache2
- 143 = used for IMAP by Dovecot
- 993 = used for IMAPS by Dovecot
- 22345 = used for SSH by Open SSH

Verify all ports listen globally on `[::]`. 
```bash
ss -6 -ltnp
```

**What this does:**
- **ss** = socket statistics, displays port information
- -t = TCP sockets
- -u = UDP sockets
- -l = Listening sockets
- -p = Show process using the port
- -e = Extended info
- -n = Don’t resolve names (show numbers like port 25)

After changing configs, restart the services.
```bash
systemctl restart nginx postfix bind9 dovecot
```

---

## Step 3: DNS AAAA Records

Edit your zone file to add ipv6 records.
```bash
nano /etc/bind/zones/db.angelo-mouawad.sasm.uclllabs.be
```

File contents to add at the end of the file.
```
; AAAA records for IPv6
ns      IN  AAAA 2001:6a8:2880:a020::d4
@       IN  AAAA 2001:6a8:2880:a020::d4
```

Increment the serial by 1 and then reload Bind.
```bash
systemctl reload bind9
```

---

## Step 4: Ensure the lab specific Assignment works

In a new terminal open this SSH Connection.
```bash
ssh -p 29422 -D 8080 -C -N root@193.191.176.212
```

Using a browser preferably Fire Fox:
- Open Settings and go to Network settings.
- Press configure how Fire Fox connects to the internet.
- Set the SOCKS Host to `127.0.0.1` and the Port to `8080`.
- Make sure SOCKS 5 is selected.

Now if you open the website go to [The Assignment](https://c-3po.uclllabs.be/sb/Lab_ipv6.php) and it will tell you your special extra port. You must now ensure your server listens on **IPv6** on port **29422**.

Create a `systemd` socket unit.
```bash
nano /etc/systemd/system/lab29422.socket
```

File contents for `/etc/systemd/system/lab29422.socket`.
```
[Unit]
Description=Dummy IPv6 listener for SaSM IPv6 lab

[Socket]
ListenStream=[::]:29422
Accept=no

[Install]
WantedBy=sockets.target
```

Create the matching service unit
```bash
nano /etc/systemd/system/lab29422.service
```

```script
[Unit]
Description=Stable IPv6 listener for SaSM port 29422
After=network-online.target
Wants=network-online.target

[Service]
ExecStartPre=/bin/sleep 5
ExecStart=/usr/bin/socat TCP6-LISTEN:29422,bind=[2001:6a8:2880:a020::d4],fork,reuseaddr -
Restart=always
RestartSec=1
User=nobody
Group=nogroup

[Install]
WantedBy=multi-user.target
```

Enable and start the new socket.
```bash
systemctl daemon-reload
systemctl enable --now lab29422.socket
```

Verify it is listening on ipv6.
```bash
ss -tulpen | grep 29422
```

---