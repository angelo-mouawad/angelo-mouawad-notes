# What is APT ?

**APT**  which stands for Advanced Package Tool is the Linux package management system. APT is what keeps a server secure, up-to-date, and consistent with the latest patches and features.

It’s responsible for:
- Installing new software with `apt install`
- Updating existing packages with `apt upgrade`
- Removing software with `apt remove`
- Managing package dependencies

The goal of this lesson is to configure a server to automatically download and install software updates without manual intervention. 

---

## Step 1: Install the Required Packages

Install `unattended-upgrades` and  `apt-listchanges`.
```bash
apt update
apt install -y unattended-upgrades apt-listchanges
```

---

## Step 2: Enable Auto Upgrades

Create and edit the configuration file.
```bash
nano /etc/apt/apt.conf.d/20auto-upgrades
```

**What this does:**
- **nano** = creates file and then opens it in nano editor

File Contents for `/etc/apt/apt.conf.d/20auto-upgrades`.
```bash
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Download-Upgradeable-Packages "1";
APT::Periodic::AutocleanInterval "7";
APT::Periodic::Unattended-Upgrade "1";
```

**What this does:**
- **Update-Package-Lists** = update `apt` database daily
- **Download-Upgradeable-Packages** = download available packages daily
- **AutocleanInterval** = remove old packages after 7 days
- **Unattended-Upgrade** = actually install updates automatically

---

## Step 3: Configure Packages to Auto Upgrade

Edit the main config file.
```bash
nano /etc/apt/apt.conf.d/50unattended-upgrades
```

Make sure the following lines are uncommented.
```bash
Unattended-Upgrade::Allowed-Origins {
        "${distro_id}:${distro_codename}";
        "${distro_id}:${distro_codename}-security";
        "${distro_id}:${distro_codename}-updates";
        "${distro_id}:${distro_codename}-proposed";
        "${distro_id}:${distro_codename}-backports";
};
```

---

## Step 4: Enable and test the service

Enable `unattended_upgrades`.
```bash
dpkg-reconfigure --priority=low unattended-upgrades
```
When prompted click `yes`.

Check if `unattended-upgrades` is active (running).
```bash
systemctl enable unattended-upgrades
systemctl start unattended-upgrades
systemctl status unattended-upgrades
```

**What this does:**
- **systemctl enable** = configures the service to start on system boot
- **systemctl start** = starts the service
- **systemctl status** = shows the current status of the service, displays running, stopped, or failed
- You should see that `unattended-upgrades` is active (running)

Test that everything works manually using a dry run.
```bash
unattended-upgrade --dry-run --debug
```

**What this does:**
- This simulates an upgrade without actually installing anything
- You should see a list of packages that would be installed

---
