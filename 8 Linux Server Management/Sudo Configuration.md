# What is Sudo ?

Sudo stands for **“superuser do”**.  It’s a Linux command that allows authorized users to execute specific commands with root privileges without needing to log in as the root user.

We use `sudo` for:
- **Security:** Users don’t need to know the root password.
- **Control:** You can specify exactly which commands a user can run as root.
- **Accountability:** All `sudo` actions are logged, so you can track who did what.
- **Safety:** Limits potential damage from mistakes or malicious commands, since users get only the permissions they truly need.

The goal of this lesson is to configure and test the `sudo` system on a Linux server and to safely grant limited administrative privileges to a normal user so it can perform system monitoring tasks without full root access.

There are two ways to give a user `sudo` privileges on Linux, the first one is by using an editing the `/etc/sudoers` file, this is the main configuration file for sudo.
```bash
sudo visudo
```

The second and better way to do it is by using the `/etc/sudoers.d/` directory. You create a separate file inside `/etc/sudoers.d/` for each user or group.
```bash
sudo nano /etc/sudoers.d/user
```

---

## Step 1: Install the Required Packages

Run these commands as **root**.
```bash
apt update
apt install -y sudo monitoring-plugins-basic net-tools
```

If the user doesn’t exist, create it.
```bash
adduser check
```

**What this does:**
- **adduser** = creates a new user
- You should set a password when prompted

---

## Step 3: Configure sudo Access for the User check

Create and edit this file.
```bash
nano /etc/sudoers.d/check
```

File contents for `/etc/sudoers.d/check`
```bash
# Allow user 'check' to run specific commands as root without password
check ALL=(ALL) NOPASSWD: /usr/lib/nagios/plugins/check_apt
check ALL=(ALL) NOPASSWD: /usr/sbin/arp
```

Set the correct permissions.
```bash
chmod 440 /etc/sudoers.d/check
chown root:root /etc/sudoers.d/check
```

Validate the configuration.
```bash
visudo -c
```

Output.
```
root@angelo:/# visudo -c
/etc/sudoers: parsed OK
/etc/sudoers.d/check: parsed OK
```

---

## Step 4: Verify the Configuration

Now switch to the `check` user and test.
```bash
su - check
```

**What this does:**
- **su** = switch user
- **-** = start a login shell

The first command should work without a password.
```bash
sudo /usr/sbin/arp -n
```

Output.
```
check@angelo:/root$ sudo /usr/sbin/arp -n
Address                  HWtype  HWaddress           Flags Mask
193.191.177.5            ether   b6:5c:06:e0:89:08   C
```

The second command should also work without a password.
```bash
sudo /usr/lib/nagios/plugins/check_apt
```

Output.
```
check@angelo:/root$ sudo /usr/lib/nagios/plugins/check_apt
APT OK: 0 packages available for upgrade (0 critical updates).
```

Disallowed command should ask for a password.
```bash
sudo ip neighbor show
```

Exit back to root.
```bash
exit
```

---