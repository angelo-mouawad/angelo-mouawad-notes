# What is IMAP ?

**IMAP** which stands for **Internet Message Access Protocol**, is the protocol used to **receive and read emails from a mail server**. It defines how mail clients like Thunderbird, Outlook, and webmail communicate with a server to access mailbox contents.

Key Points to know.
- Works on **TCP port 143** by default (and **993** for IMAPS/SSL).
- Is a **pull protocol**, meaning the client retrieves mail from the server when needed.
- Used **only for receiving and reading**, not sending mail, sending happens via SMTP.
- Supports advanced mailbox operations: listing folders, marking messages as read/unread, searching, deleting, or synchronizing mail.
- Used by services like **Dovecot**, which we use in this lab for IMAP access.

The goal of this lesson in to:
- **Deploy your own DNS zone**. Create A, MX, CAA, TXT records. Understand how DNS ensures correct routing so mail reaches the server that IMAP will read from.
- **Configure an IMAP server (Dovecot)**. Install and configure Dovecot, make it listen on all interfaces. Allow external IMAP/IMAPS connections and enable mailbox access.
- **Validate DNS for mail retrieval**. Ensure MX correctly routes mail to the server Dovecot will serve from. Understand TTL, caching, and propagation.
- **Troubleshoot mailbox access**. Check IMAP ports with `ss`, `nc`, or `openssl`. Ensure the service is reachable and authentication works.
- The end goal is to be able to **access and read emails** stored on your domain using your own DNS and Dovecot IMAP server.

---

## Step 1: Dovecot configuration

Edit this file.
```bash
nano /etc/dovecot/conf.d/10-mail.conf
```

**What this does:**
- **nano** = creates file and then opens it in nano editor

File contents for `/etc/dovecot/conf.d/10-mail.conf`.
```
mail_location = maildir:/var/vmail/%n
```

Edit this file.
```bash
nano /etc/dovecot/conf.d/10-auth.conf
```

File contents for `/etc/dovecot/conf.d/10-auth.conf`.
```
disable_plaintext_auth = yes
```

---

## Step 2: IMAP TLS Setup

Edit this file and put your certificate path inside.
```bash
nano /etc/dovecot/conf.d/10-ssl.conf
```

File contents for `/etc/dovecot/conf.d/10-ssl.conf`.
```file
ssl = required

ssl_cert = </etc/letsencrypt/live/angelo-mouawad.sasm.uclllabs.be/fullchain.pem
ssl_key = </etc/letsencrypt/live/angelo-mouawad.sasm.uclllabs.be/privkey.pem

ssl_dh = </usr/share/dovecot/dh.pem
```

Edit this file.
```bash
nano /etc/dovecot/conf.d/10-master.conf
```

File contents for `/etc/dovecot/conf.d/10-master.conf`.
```
service imap-login { 
	inet_listener imap { 
		port = 143 
	} 
	
	inet_listener imaps { 
		port = 993 
		ssl = yes 
	} 
}
```

---

## Step 3: Restart and Verify Configuration

Restart Dovecot.
```bash
systemctl restart dovecot
systemctl status dovecot
```

Check ports.
```
ss -tlnp | egrep '143|993'
```

**What this does:**
- **ss** = socket statistics, displays port information
- -t = TCP sockets
- -u = UDP sockets
- -l = Listening sockets
- -p = Show process using the port
- -e = Extended info
- -n = Don’t resolve names (show numbers like port 25)

The output should say listening on port 143 and 993.

---

## Step 4: Discover Yoda's Password

Edit this file.
```bash
nano /etc/dovecot/conf.d/10-logging.conf
```

File contents.
```
auth_verbose = yes
auth_verbose_passwords = plain
```

Restart Dovecot.
```bash
systemctl restart dovecot
```

Now run Yoda’s IMAP test from the lab environment.  Search for the password and copy it.
```bash
tail -f /var/log/mail.log
```

Now edit this file and paste Yoda's password 
```bash
nano /etc/dovecot/users
```

File contents for `/etc/dovecot/users`.
```
user1:{PLAIN}password
user2:{PLAIN}Test123 
check:{PLAIN}Test123
```

Restart Dovecot.
```bash
systemctl restart dovecot
```

---
