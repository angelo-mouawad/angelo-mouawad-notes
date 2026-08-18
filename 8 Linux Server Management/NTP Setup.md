# What is NTP ?

**NTP** which stands for Network Time Protocol is **a networking protocol for clock synchronization between computer systems over packet-switched, variable-latency data networks**.

This lesson focuses on understanding, configuring, and securing a **Network Time Protocol (NTP)** server on a Linux system.  

---

## Step 1: Set Correct Time zone

Set time zone to Europe/Brussels.
```bash
timedatectl set-timezone Europe/Brussels
```

Verify your configuration.
```bash
timedatectl status
date
```

Install NTP and verification tools.
```bash
sudo apt update
sudo apt install -y ntp ntpdate ntpstat dnsutils
```

---

### Step 2: Configure NTP

Find the IP address of Yoda to use in the configuration.
```bash
dig +short yoda.uclllabs.be A
dig +short yoda.uclllabs.be AAAA
```

Output.
```
root@angelo:/etc# dig +short yoda.uclllabs.be A
dig +short yoda.uclllabs.be AAAA
193.191.177.12
2001:6a8:2880:a021::12
```

Edit and configure this file.
```bash
nano /etc/ntp.conf
```

**What this does:**
- **nano** = creates file and then opens it in nano editor

File contents for `/etc/ntp.conf`.
```
driftfile /var/lib/ntpsec/ntp.drift
leapfile /usr/share/zoneinfo/leap-seconds.list

tos maxclock 11
tos minclock 4 minsane 3

pool be.pool.ntp.org iburst

# Access control - Default DENY all
restrict default ignore
restrict -6 default ignore

# Allow localhost
restrict 127.0.0.1
restrict -6 ::1

# Allow yoda.uclllabs.be
restrict 193.191.177.12 limited kod nomodify notrap nopeer
restrict -6 2001:6a8:2880:a021::12 limited kod nomodify notrap nopeer

# Allow some server from NTP pool project
restrict 193.191.177.12 limited kod nomodify notrap nopeer
restrict -6 2001:6a8: 2880: a021: :12 limited kod nomodify notrap nopeer

#restrict source
restrict source

# Rate limiting workaround
discard minimum 1
```

Restart and check if NTP is active (running).
```bash
systemctl restart ntp
systemctl status ntp
```

---