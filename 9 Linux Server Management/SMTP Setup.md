# What is a SMTP ?

**SMTP** which stands for Simple Mail Transfer Protocol is the protocol used to **send emails across the Internet**. It defines how mail servers talk to each other and how emails are transferred.

Key Points to know.
-  Works on **TCP port 25** by default.
- Is a **push protocol** which means servers push mail to the next hop.
- Used **only for sending**, not receiving or reading mail, receiving happens via IMAP.
- Follows a simple text-based command structure: `HELO`, `MAIL FROM`, `RCPT TO`, and `DATA`.
- Used by services like Postfix which we use in this lab.

The goal of this lesson is to:
- **Deploy your own DNS zone**. Create A, MX, CAA, TXT records. Understand how DNS supports email delivery.
- **Configure an SMTP server (Postfix)**. Install and configure Postfix, make it listen on all interfaces. Allow external SMTP connections and ensure proper mail routing.
- **Validate DNS for email**. Use `dig` to check MX record. Ensure MX points to the correct host. Understand TTL and propagation.
- **Troubleshoot email delivery**. Check SMTP port with `ss` or `nc`.
- The end goal is to be able to **receive an email** at your domain using your own DNS + Postfix server.

---

## Step 1: Install and Basic Setup

Display what's currently configured.
```bash
apt update
apt install -y postfix dovecot-imapd dovecot-lmtpd mailutils
```

Edit your zone file.
```bash
nano /etc/bind/zones/db.angelo-mouawad.sasm.uclllabs.be
```

**What this does:**
- **nano** = creates file and then opens it in nano editor

File contents to be added to the end of the file.
```
; A records
@       IN  MX  10 mx.angelo-mouawad.sasm.uclllabs.be.
mx      IN  A   193.191.176.212
mail    IN  A   193.191.176.212
```

Increment the serial by 1 before saving. Then reload bind.
```bash
systemctl reload bind9
```

Confirm and verify.
```bash
dig @127.0.0.1 MX angelo-mouawad.sasm.uclllabs.be +short
dig @127.0.0.1 A mx.angelo-mouawad.sasm.uclllabs.be +short
```

**What this does:**
- **dig** = domain information groper, used to query `dns` servers 

Output.
```output
root@angelo:/#
dig @127.0.0.1 MX angelomouawad.sasm.uclllabs.be +short
dig @127.0.0.1 A mx.angelo-mouawad.sasm.uclllabs.be +short
10 mx.angelo-mouawad.sasm.uclllabs.be. 
193.191.176.212
```

---

### Step 2: Create V Mail User

Create `vmail` user and group.
```bash
groupadd -g 2000 vmail
useradd -u 2000 -g vmail -d /var/vmail -m -s /usr/sbin/nologin vmail
```

Prepare the mail directories.
```bash
mkdir -p /var/vmail/user1 /var/vmail/user2 /var/vmail/check
chown -R vmail:vmail /var/vmail
chmod -R 700 /var/vmail
```

**What this does:**
- **mkdir** = make directory 
- **chmod** = changes who can read, write, or execute a file
- **chown** = changes owner of the file 

---

## Step 3: Postfix configuration

Run these commands one by one to configure postfix settings.
```bash
postconf -e "myhostname = mx.angelo-mouawad.sasm.uclllabs.be" 
postconf -e "mydomain = angelo-mouawad.sasm.uclllabs.be" 
postconf -e "myorigin = \$mydomain"
postconf -e "mydestination ="
postconf -e "mynetworks = 127.0.0.0/8 [::1]/128 10.0.0.0/8 172.16.0.0/12 1 92.168.0.0/16"
postconf -e "virtual_mailbox_domains = angelo-mouawad.sasm.uclllabs. be"
postconf -e "virtual_mailbox_base = /var/vmail" 
postconf -e "virtual_mailbox_maps = hash:/etc/postfix/vmailbox" 
postconf -e "virtual_uid_maps = static:2000" 
postconf -e "virtual_gid_maps = static:2000" 
postconf -e "virtual_minimum_uid = 2000"
postconf -e "virtual_transport = lmtp:unix:private/dovecot-lmtp" 
postconf -e "smtpd_recipient_restrictions = permit_mynetworks, reject_ unauth_destination"
postconf -e "smtpd_tls_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem" 
postconf -e "smtpd_tls_key_file=/etc/ssl/private/ssl-cert-snakeoil.key" 
postconf -e "smtpd_tls_security_level=may"
postconf -e "smtp_tls_security_level=may"
```

Edit this file.
```bash
nano /etc/mailname
```

File contents for `/etc/mailname`.
```
angelo-mouawad.sasm.uclllabs.be
```

Edit the virtual mailbox map file.
```bash
nano /etc/postfix/vmailbox
```

File contents for `/etc/postfix/vmailbox`.
```
user1@angelo-mouawad.sasm.uclllabs.be user1/
user2@angelo-mouawad.sasm.uclllabs.be user2/
check@angelo-mouawad.sasm.uclllabs.be check/
```

Build the map.
```bash
postmap /etc/postfix/vmailbox
postmap -s /etc/postfix/vmailbox
```

Edit this file.
```bash
nano /etc/postfix/main.cf
```

File contents for `/etc/postfix/main.cf`.
```
inet_interfaces = all
```

Restart Postfix.
```bash
systemctl restart postfix
```

Validate Postfix configuration.
```bash
postconf -n | egrep 'myhostname|virtual_mailbox_domains|virtual_mailbox_m aps|mynetworks|mydestination'
```

Output.
```output
root@angelo:/# postconf -n | egrep 'myhostname|virtual_mailbox_domains|virtual_mailbox_m aps|mynetworks|mydestination'
myhostname = mx.angelo-mouawad.sasm.uclllabs.be 
virtual_mailbox_domains = angelo-mouawad.sasm.uclllabs.be
virtual_mailbox_maps = hash:/etc/postfix/vmailbox 
mydestination =
```

---

## Step 4: Dovecot Configuration

Edit this file.
```bash
nano /etc/dovecot/dovecot.conf
```

File contents for `/etc/dovecot/dovecot.conf`.
```
protocols = imap lmtp 
mail_location = maildir:/var/vmail/%n
```

Edit this file.
```bash
nano /etc/dovecot/users
```

File contents for.
```
user1:{PLAIN}Test123 
user2:{PLAIN}Test123 
check:{PLAIN}Test123
```

Set the correct permissions.
```bash
chown root:dovecot /etc/dovecot/users
chmod 640 /etc/dovecot/users
```

Edit this file and enable passwd-file authentication.
```bash
nano /etc/dovecot/conf.d/auth-passwdfile.conf.ext
```

File contents for `/etc/dovecot/conf.d/auth-passwdfile.conf.ext`.
```file
passdb {
	driver = passwd-file
	args = scheme=PLAIN username_format=%n /etc/dovecot/users
}

userdb { 
	driver = static 
	args = uid=vmail gid=vmail home=/var/vmail/%n
}
```

Edit the file.
```bash
nano /etc/dovecot/conf.d/10-auth.conf
```

File contents for `/etc/dovecot/conf.d/10-auth.conf`.
```
disable_plaintext_auth = no 
auth_mechanisms = plain login 
!include auth-passwdfile.conf.ext
```

Edit the file.
```bash
nano /etc/dovecot/conf.d/10-master.conf
```

File contents for `/etc/dovecot/conf.d/10-master.conf`.
```
service lmtp { 
	unix_listener /var/spool/postfix/private/dovecot-lmtp {
		mode = 0600
		user = postfix
		group = postfix
	}
}
```

---