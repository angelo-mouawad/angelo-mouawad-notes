# What is SSH ?

SSH (Secure Shell) is a network protocol that provides a secure, encrypted channel for logging into remote machines and running commands, transferring files, and tunneling network services.
Key points:

- **Encryption & authentication**: protects login credentials and the session, you can authenticate with passwords _or_ public/private key pairs.
- **Portability**: available on almost every OS (Linux, macOS, Windows).
- **Uses**: remote shell, file transfer (SCP/SFTP), port forwarding (tunneling), remote command execution, and secure administration.

The first main concept is connecting to a machine.
```bash
ssh user@machineIp
```

Command options.
- `-p port` Specifies the port you want to connect over.
- `-f fileName` Specifies the private key file path that should be used.
- `-t` Forces terminal connection.
- `-T` Disables terminal connection.

So logging into your server would look like this.
```bash
ssh -p 22345 root@193.191.176.212
```

Now moving on to the second main concept which is generating a key pair.
```bash
ssh-keygen
```

Commands options.
- `-t type` Specifies the key type like `rsa`, `ed25519`, or `ecdsa` (the default is `rsa`).
- `-b bits` Sets the key length in bits.
- `-C comment` Adds a comment to the key like your email for more security.
- `-f fileName` Specifies the output file path in case you want custom names. When specifying the path in this option, in PowerShell, should use `C:\Users\angel\.ssh` instead of `~/.ssh`.

When you use execute this command without specifying the file name `-f filename` , you will see.
```
Generating public/private rsa key pair.
Enter file in which to save the key (/home/username/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```

File location: Your key pair is saved in your SSH directory by default if a location is not specified.
- The private key will be saved in `~/.ssh/id_rsa`.
- The public key will be saved in `~/.ssh/id_rsa.pub`.

Passphrase: You can optionally add a passphrase for extra protection.
- If you add one, you’ll need to type it when using the key (like a password for your key). 
- If you leave it blank by pressing Enter, you’ll be able to use the key without typing anything.

---

## Step 1: Change the Default port

Override `systemd` socket. Create override file. This step is not always required.
```bash
mkdir -p /etc/systemd/system/ssh.socket.d/
nano /etc/systemd/system/ssh.socket.d/override.conf
```

**What this does:**
- **mkdir** = make directory
- **-p** = makes sure parents directory in the path are also created
-  **nano** = creates file and then opens it in nano editor

File contents for `/etc/systemd/system/ssh.socket.d/override.conf`.
```
[Socket]
ListenStream= 
ListenStream=0.0.0.0:22345 
ListenStream=[::]:22345
```

Reload and restart socket.
```bash
systemctl daemon-reload
systemctl restart ssh.socket
systemctl status ssh.socket --no-pager
```

Verify listening port.
```bash
ss -ltnp | grep 22345
ss -ltnp | grep ssh
```

Edit this file.
```bash
nano /etc/ssh/sshd_config
```

File contents for `/etc/ssh/sshd_config`.
```bash
UseDNS no
Port 22345
```

Verify the syntax.
```bash
sshd -t
```

**What this does:**
- **sshd** = the SSH daemon
- **-t** = test mode and syntax check, it checks the configuration file `/etc/ssh/sshd_config` for errors without starting the daemon

Exit your server. 
```bash
exit
```

Try to log back into your server.
```bash
ssh -p 22345 root@193.191.176.212
```

---

## Step 2: SSH Key Pair Setup

On your personal machine install SSH.
```bash
apt update
apt install openssh-client
```

On your personal machine generate SSH Key pair without setting a passphrase.
```bash
ssh-keygen
```

**What this does:**
- This command creates a new SSH key pair (a public and private key).
- Both keys will be saved in the `/.ssh` directory unless you specify another directory.

Read the public key file.
```
cat ~/.ssh/id_rsa.pub
```

**What this does:**
- **cat** = reads the file
- You should copy your public key when it is displayed

On the server, make the `.ssh` directory if its not already there.
```bash
mkdir -p ~/.ssh chmod 700 ~/.ssh
```

Create and edit this file. Paste your public key inside.
```bash
nano ~/.ssh/authorized_keys
```

Set the correct permissions.
```bash
chmod 600 ~/.ssh/authorized_keys
```

Verify your configuration.
```bash
ls -ld ~/.ssh ~/.ssh/authorized_keys
```

What this does:
- **ls** = list files and directories
- **-l** = use long format, showing details like permissions, owner, group, size, and modification date
- **-d** = list the directory itself, not its contents

Output.
```
root@angelo:/# ls -ld ~/.ssh ~/.ssh/authorized_keys
drwx------ 2 user users 4096 Oct 4 14:30 .ssh 
-rw------- 1 user users 400 Oct 4 14:31 .ssh/authorized_keys
```

Restart SSH.
```bash
sudo systemctl restart ssh
```

---

## Step 3: Creating the check User for SSH Access

Create the user.
```bash
useradd -m -s /bin/bash check
```

**What this does:**
- **useradd** = creates a new user
- You should set a password when prompted

Create the `.shh` directory for the check user.
```bash
mkdir -p /home/check/.ssh
```

Set the correct permissions.
```bash
chown check:check /home/check/.ssh
chmod 700 /home/check/.ssh
```

Create and edit this file as the check user.
```bash
sudo -u check nano /home/check/.ssh/authorized_keys
```

What this does:
- **sudo** = run this command with administrator privileges
- **-u** = runs a single command as another user

File contents for `/home/check/.ssh/authorized_keys`.
```bash
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAw2YPreIBDz/BbRF8ftteme4wyV8T6aNc9TLNY4Xk7K2ta9pPWux7g5fnwnMv/WVBMLbYPh3ECX8G95OeUDGk5UgefjZiBqyAqUFmekzQnOcfhy6aiSc1xe8r0dEMF10Fj3Duvy18Vc0yPQMQCQPkBgr/7n4dxfBdXJsp/GF2p4bLVzKSNoRho0msZEaX/QuCcOgntRzLBtr7+HpVxoCOsTQ9njeC4FBEmKx4soxQG7u4EZI2ZZAVRGYXVANiYodXjgGwQTMTO2pKJzu8s0SK6JcRouGMVdPORf9VoFq2V8YjbhAtrrbkGCemJtXltsxiUe7w5V+8GGGQvigCOo6Gmw== Patience-you-must-have-my-young-Padawan@yoda
```

Set the correct permissions.
```bash
chown check:check /home/check/.ssh/authorized_keys
chmod 600 /home/check/.ssh/authorized_keys
```

---

## Step 4: Setup SSH Guard

Install SSH Guard.
```bash
apt update
apt install sshguard
```

Find the Yoda server IP addresses and copy them.
```bash
dig +short yoda.uclllabs.be A     # IPv4
dig +short yoda.uclllabs.be AAAA  # IPv6
```

Output. 
```
root@angelo:/# dig +short yoda.uclllabs.be A
dig +short yoda.uclllabs.be AAAA
193.191.177.12
2001:6a8:2880:a021::12
```

Edit this file and then paste the Yoda server IP addresses.
```bash
nano /etc/sshguard/whitelist
```

---

## Step 5: SSH Guard Configuration

Edit the SSH Guard configuration file.
```bash
nano /etc/sshguard/sshguard.conf
```

File contents for `/etc/sshguard/sshguard.conf`.
```script
#### REQUIRED CONFIGURATION ####
# Full path to backend executable (required, no default)
BACKEND="/usr/libexec/sshguard/sshg-fw-nft-sets"

# Shell command that provides logs on standard output. (optional, no default)
# Example 1: ssh and sendmail from systemd journal:
LOGREADER="LANG=C journalctl -afb -p info -n1 -t sshd -o cat"

#### OPTIONS ####
# Block attackers when their cumulative attack score exceeds THRESHOLD.
# Most attacks have a score of 10. (optional, default 30)
THRESHOLD=50

# Block attackers for initially BLOCK_TIME seconds after exceeding THRESHOLD.
# Subsequent blocks increase by a factor of 1.5. (optional, default 120)
BLOCK_TIME=300

# Remember potential attackers for up to DETECTION_TIME seconds before
# resetting their score. (optional, default 1800)
DETECTION_TIME=120

# IP addresses listed in the WHITELIST_FILE are considered to be
# friendlies and will never be blocked.
WHITELIST_FILE=/etc/sshguard/whitelist
```

Reload SSH Guard.
```bash
systemctl daemon-reload
systemctl restart sshguard
systemctl status sshguard
```

---