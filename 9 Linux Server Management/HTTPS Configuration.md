# What Is HTTPS ?

HTTPS stand for Hyper Text Transfer Protocol Secure, it is the secure version of HTTP, the protocol used by web browsers to communicate with websites.
- HTTPS **encrypts data** sent between your browser and a website, so sensitive information like, passwords, messages, or credit card numbers, cannot be intercepted by attackers.
- It uses **SSL/TLS certificates** to confirm that the website is authentic, ensuring that you are really connecting to the intended server and not an impostor.
- When you visit a site with HTTPS, you usually see a **padlock icon** in your browser’s address bar.

The goal of this this lesson is to set up HTTPS on your Apache server. Create virtual hosts for your subdomains, `secure` and `supersecure`. Ensure all HTTP traffic is redirected to HTTPS. Additionally you have to obtain a valid SSL/TLS certificate from Let’s Encrypt using DNS verification. Certificates prove your website is authentic. DNS-01 challenge means you prove ownership by adding special TXT records to your DNS zone.

---

## Step 1: Install Required Packages 

Required packages.
```bash
apt update
apt install -y apache2 certbot python3-certbot-apache bind9-utils dnsutils httpie
```

Verify Apache and modules.
```bash
systemctl status apache2
a2enmod ssl
a2enmod headers
systemctl reload apache2
```

Confirm DNS A records for your FQDN and subdomains.
```bash
dig +short A angelo-mouawad.sasm.uclllabs.be
dig +short A secure.angelo-mouawad.sasm.uclllabs.be
dig +short A supersecure.angelo-mouawad.sasm.uclllabs.be
```

**What this does:**
- **dig** = domain information groper, used to query `dns` servers 

If any doesn't return your IP, add the A record in your Bind9 zone.
```bash
sudo -u check sudo -n dns_add_record -t A secure 193.191.176.212 angelo-mouawad.sasm.uclllabs.be
sudo -u check sudo -n dns_add_record -t A supersecure 193.191.176.212 angelo-mouawad.sasm.uclllabs.be
```

---

## Step 2: Create the Apache virtual hosts

Create HTTP `vhost` for `secure.angelo-mouawad.sasm.uclllabs.be` that redirects to HTTPS.
```bash
nano /etc/apache2/sites-available/secure.angelo-mouawad.sasm.uclllabs.be.conf
```

**What this does:**
- **nano** = creates file and then opens it in nano editor

File contents.
```file
<VirtualHost *:80>
    ServerName secure.angelo-mouawad.sasm.uclllabs.be
    DocumentRoot /var/www/secure

    # Simple permanent redirect to HTTPS
    Redirect permanent / https://secure.angelo-mouawad.sasm.uclllabs.be/
    ErrorLog ${APACHE_LOG_DIR}/secure-error.log
    CustomLog ${APACHE_LOG_DIR}/secure-access.log combined
</VirtualHost>
```

Create HTTP `vhost` for `supersecure.angelo-mouawad.sasm.uclllabs.be`.
```bash
nano /etc/apache2/sites-available/supersecure.angelo-mouawad.sasm.uclllabs.be.conf
```

File contents.
```file
<VirtualHost *:80>
    ServerName supersecure.angelo-mouawad.sasm.uclllabs.be
    DocumentRoot /var/www/supersecure

    # Redirect to the HTTPS site (first-time redirect)
    Redirect temp / https://supersecure.angelo-mouawad.sasm.uclllabs.be/

    ErrorLog ${APACHE_LOG_DIR}/supersecure-error.log
    CustomLog ${APACHE_LOG_DIR}/supersecure-access.log combined
</VirtualHost>

```

---

## Step 3: Enabling Sites 

Create placeholder directories and a simple test index.
```bash
mkdir -p /var/www/secure /var/www/supersecure
echo "<h1>secure site (http redirect)</h1>" | sudo tee /var/www/secure/index.html
echo "<h1>supersecure site (http redirect)</h1>" | sudo tee /var/www/supersecure/index.html
chown -R www-data:www-data /var/www/secure /var/www/supersecure
```

**What this does:**
- **chown** = changes owner of the file 

Enable the sites and reload Apache.
```bash
a2ensite secure.angelo-mouawad.sasm.uclllabs.be.conf
a2ensite supersecure.angelo-mouawad.sasm.uclllabs.be.conf
systemctl reload apache2
```

---

## Step 4: Zone Configuration and Fixing

Edit your zone file.
```bash
nano /etc/bind/zones/db.angelo-mouawad.sasm.uclllabs.be
```

File contents to be added to the end of the file.
```
; A records
secure  IN  A   193.191.176.212
supersecure IN A 193.191.176.212

; CAA – allow Let's Encrypt for this zone
@   IN  CAA 0 issue "letsencrypt.org"
@   IN  CAA 0 iodef "mailto:angelo.mouawad@student.ucll.be"
```

Before saving, increment your serial by 1.

Verify and reload DNS.
```bash
systemctl reload bind9
rndc reload angelo-mouawad.sasm.uclllabs.be
```

Check and verify.
```bash
dig +short secure.angelo-mouawad.sasm.uclllabs.be
dig +short supersecure.angelo-mouawad.sasm.uclllabs.be
```

---

## Step 5: Prepare to obtain a Let’s Encrypt certificate

Run the following in the terminal.
```bash
certbot certonly --manual --preferred-challenges dns --staging \
  -d angelo-mouawad.sasm.uclllabs.be \
  -d secure.angelo-mouawad.sasm.uclllabs.be \
  -d supersecure.angelo-mouawad.sasm.uclllabs.be
```

When you run this command you will be shown three tokens, you will need to press enter twice to see all three. When you get to the third token don't press enter because that will end the prompt.

Now open a new terminal into your server and edit your zone file.
```bash
nano /etc/bind/zones/db.angelo-mouawad.sasm.uclllabs.be
```

Add your tokens like this at the end and increment the serial by 1.
```
; Let’s Encrypt DNS-01 challenge tokens
_acme-challenge           IN TXT "token1"
_acme-challenge.secure    IN TXT "token2"
_acme-challenge.supersecure IN TXT "token3"
```

Reload DNS.
```bash
rndc reload angelo-mouawad.sasm.uclllabs.be
```

Now you can go back to your first terminal and press enter until the prompt is completed.

Verify DNS TXT is visible externally
```bash
dig TXT +short _acme-challenge.angelo-mouawad.sasm.uclllabs.be
dig TXT +short _acme-challenge.secure.angelo-mouawad.sasm.uclllabs.be
dig TXT +short _acme-challenge.supersecure.angelo-mouawad.sasm.uclllabs.be
```

---

## Step 6: Install certificate in Apache SSL virtual hosts

Create and edit the first SSL `vhost`.
```bash
nano /etc/apache2/sites-available/secure.angelo-mouawad.sasm.uclllabs.be-ssl.conf
```

File contents.
```file
<IfModule mod_ssl.c>
<VirtualHost *:443>
    ServerName secure.angelo-mouawad.sasm.uclllabs.be
    DocumentRoot /var/www/secure

    SSLEngine on
    SSLCertificateFile /etc/letsencrypt/live/angelo-mouawad.sasm.uclllabs.be/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/angelo-mouawad.sasm.uclllabs.be/privkey.pem

    # HTTP security headers (recommended)
    Header always set X-Content-Type-Options "nosniff"
    Header always set X-Frame-Options "DENY"

    ErrorLog ${APACHE_LOG_DIR}/secure-ssl-error.log
    CustomLog ${APACHE_LOG_DIR}/secure-ssl-access.log combined
</VirtualHost>
</IfModule>

```

Create and edit the second SSL `vhost`.
```bash
nano /etc/apache2/sites-available/supersecure.angelo-mouawad.sasm.uclllabs.be-ssl.conf
```

```file
<IfModule mod_ssl.c>
<VirtualHost *:443>
    ServerName supersecure.angelo-mouawad.sasm.uclllabs.be
    DocumentRoot /var/www/supersecure

    SSLEngine on
    SSLCertificateFile /etc/letsencrypt/live/angelo-mouawad.sasm.uclllabs.be/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/angelo-mouawad.sasm.uclllabs.be/privkey.pem

    # Send HSTS the first time clients connect via HTTPS
    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"

    Header always set X-Content-Type-Options "nosniff"
    Header always set X-Frame-Options "DENY"

    ErrorLog ${APACHE_LOG_DIR}/supersecure-ssl-error.log
    CustomLog ${APACHE_LOG_DIR}/supersecure-ssl-access.log combined
</VirtualHost>
</IfModule>
```

Enable these sites and reload Apache.
```bash
a2ensite secure.angelo-mouawad.sasm.uclllabs.be-ssl.conf
a2ensite supersecure.angelo-mouawad.sasm.uclllabs.be-ssl.conf
systemctl reload apache2
```

---