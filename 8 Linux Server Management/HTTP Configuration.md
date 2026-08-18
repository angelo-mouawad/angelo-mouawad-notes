# What Is HTTP ?

HTTP stand for Hyper Text Transfer Protocol, it is the protocol used by browsers to communicate with websites.

The goal of this lesson is to install, configure, and manage an Apache web server with multiple virtual hosts, automate `vhost` creation, and ensure proper security and logging. A **virtual host allows one Apache server to host multiple websites on the same server**.

First you should install and configure Apache or Nginx. The default webpage must display the word **“welcome”** for any undefined or non-existing host.

Next you have to configure a couple virtual Hosts. The first one www1, should display the string **“www1”** and **must not contain “welcome”**. The second one www2, must have a PHP page `toupper.php` that converts the `code` query parameter to uppercase. Directory browsing must be disabled.

Then you have to setup **security**. All pages in the `private` directory of `www1` must be password-protected using `.htaccess` and **basic authentication**. User `check` must be able to access these pages with password `ch3ck`.

Scripts are stored in `/etc/scripts/`. Only **root** can edit the scripts. User `check` can run the script `http_add_vhost` via `sudo` to create `vhosts`. Scripts must validate input, refuse non-existing domains, and create proper document roots, index pages, and logging.

Finally you have to setup the **cleanup** process. Automatically remove `vhosts` older than 4 hours, including configuration files, document roots, and log files. This ensures a clean environment as many `vhosts` are automatically created over time.

---

## Step 1: Install Required Packages 

Required packages.
```bash
apt update
apt install -y apache2 php
```

Enable and start Apache.
```bash
systemctl enable apache2
systemctl start apache2
systemctl status apache2
```

Set your server’s hostname
```bash
hostnamectl set-hostname angelo-mouawad.sasm.uclllabs.be
```

Edit your hosts file.
```bash
nano /etc/hosts
```

**What this does:**
- **nano** = creates file and then opens it in nano editor

File contents for `/etc/hosts`.
```
193.191.176.212   angelo-mouawad.sasm.uclllabs.be
```

---

## Step 2: Configure your default virtual host

Edit the default configuration.
```
nano /etc/apache2/sites-available/000-default.conf
```

File contents `/etc/apache2/sites-available/000-default.conf`.
```
<VirtualHost *:80>
        DocumentRoot /var/www/html
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Make sure the file contains the above content.

Create and an index page.
```bash
nano /var/www/html/index.html
```

File contents for `/var/www/html/index.html`.
```
welcome
```

If the page has default configuration, replace it all with the above content.

Restart Apache.
```bash
systemctl restart apache2
```

Test and check if the page returns welcome.
```bash
curl http://angelo-mouawad.sasm.uclllabs.be/
```

**What this does:**
- **curl** = calls a webpage or URL and returns the contents

---

## Step 3: Create Virtual Host www1

Create Document Root.
```bash
mkdir -p /var/www/html/www1
```

**What this does:**
- **mkdir** = make directory 

Create index page.
```bash
nano /var/www/html/www1/index.html
```

File contents for `/var/www/html/www1/index.html`.
```
www1
```

Create `vhost` config.
```bash
nano /etc/apache2/sites-available/www1.angelo-mouawad.sasm.uclllabs.be.conf
```

File contents.
```
<VirtualHost *:80>
        ServerName www1.angelo-mouawad.sasm.uclllabs.be
        DocumentRoot /var/www/html/www1
        ErrorLog ${APACHE_LOG_DIR}/www1-error.log
        CustomLog ${APACHE_LOG_DIR}/www1-access.log combined

        <Directory /var/www/html/www1/private>
                AuthType Basic
                AuthName "Restricted Access"
                AuthUserFile /etc/apache2/.htpasswd
                Require valid-user
        </Directory>
</VirtualHost>
```

Enable `vhost`.
```bash
a2ensite www1.angelo-mouawad.sasm.uclllabs.be.conf
systemctl reload apache2
```

Test and verify.
```bash
curl http://www1.angelo-mouawad.sasm.uclllabs.be/
```

---

## Step 4: Create Virtual Host www2

Create Document Root.
```bash
mkdir -p /var/www/html/www2
```

Create `toupper.php`.
```bash
nano /var/www/html/www2/toupper.php
```

File contents for `/var/www/html/www2/toupper.php`.
```php
<?php
if(isset($_GET['code'])){
    echo strtoupper($_GET['code']);
} else {
    echo "No code provided.";
}
?>
```

Create `vhost` config.
```bash
nano /etc/apache2/sites-available/www2.angelo-mouawad.sasm.uclllabs.be.conf
```

File contents.
```
<VirtualHost *:80>
    ServerName www2.angelo-mouawad.sasm.uclllabs.be
    DocumentRoot /var/www/html/www2
    ErrorLog ${APACHE_LOG_DIR}/www2-error.log
    CustomLog ${APACHE_LOG_DIR}/www2-access.log combined

    <Directory /var/www/html/www2>
        Options -Indexes
        AllowOverride None
    </Directory>
</VirtualHost>
```

Enable `vhost`.
```bash
a2ensite www2.angelo-mouawad.sasm.uclllabs.be.conf
systemctl reload apache2
```

Test and verify that the page returns `ABCDEF`.
```bash
curl "http://www2.angelo-mouawad.sasm.uclllabs.be/toupper.php?code=AbCdEf"
```

---

## Step 5: Protect private directory in www1

Create private directory.
```bash
mkdir /var/www/html/www1/private
```

Create `.htpasswd` user.
```bash
sudo htpasswd -c /etc/apache2/.htpasswd check
```

Enable `.htaccess` in www1.
```bash
nano /etc/apache2/sites-available/www1.angelo-mouawad.sasm.uclllabs.be.conf
```

File contents inside `<Directory>` for `/var/www/html/www1`.
```
<Directory /var/www/html/www1/private>
    AuthType Basic
    AuthName "Restricted Access"
    AuthUserFile /etc/apache2/.htpasswd
    Require valid-user
</Directory>
```

Reload Apache.
```bash
systemctl reload apache2
```

Test with curl.
```bash
curl -u check:ch3ck http://www1.angelo-mouawad.sasm.uclllabs.be/private/
```

---

## Step 6: Create the add virtual host Script

Create the script.
```bash
nano /etc/scripts/http_add_vhost
```

File contents for `/etc/scripts/http_add_vhost`.
```script
#!/bin/bash

# Check if running as sudo
if [ "$EUID" -ne 0 ]; then
  echo "You must run this script with sudo"
  exit 1
fi

if [ -z "$1" ]; then
  echo "Usage: sudo http_add_vhost <vhost_name>"
  exit 1
fi

VHOST=$1
DOCROOT="/var/www/html/$VHOST"
CONF="/etc/apache2/sites-available/$VHOST.conf"

# Refuse non-existing domain
if ! ping -c 1 "$VHOST" &> /dev/null; then
  echo "Domain $VHOST does not exist"
  exit 1
fi

# Create docroot
mkdir -p "$DOCROOT"

# Create index.html
echo "welcome $VHOST" > "$DOCROOT/index.html"

# Create vhost config
cat <<EOF > "$CONF"
<VirtualHost *:80>
    ServerName $VHOST
    DocumentRoot $DOCROOT
    ErrorLog \${APACHE_LOG_DIR}/$VHOST-error.log
    CustomLog \${APACHE_LOG_DIR}/$VHOST-access.log combined
</VirtualHost>
EOF

# Enable site and reload
a2ensite "$VHOST.conf"
systemctl reload apache2
echo "Vhost $VHOST created successfully."
```

Set the correct permissions.
```bash
chown root:root /etc/scripts/http_add_vhost
chmod 700 /etc/scripts/http_add_vhost
chmod 500 /etc/scripts/http_add_vhost
```

**What this does:**
- **chmod** = changes who can read, write, or execute a file
- **chown** = changes owner of the file 

---

## Step 7: Allow the user check to run the script

Edit and create this file.
```bash
nano /etc/sudoers.d/http-lab
```

File contents for `/etc/sudoers.d/http-lab`.
```file
Defaults secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin:/etc/scripts"

Cmnd_Alias HTTP_LAB_CMDS = \
    /etc/scripts/http_add_vhost

check ALL=(root) NOPASSWD: HTTP_LAB_CMDS
```

Set the correct permissions.
```bash
chown root:root /etc/sudoers.d/http-lab
chmod 440 /etc/sudoers.d/http-lab
```

Test and verify.
```bash
sudo -u check sudo /etc/scripts/http_add_vhost test.angelo-mouawad.sasm.uclllabs.be
```

---

## Step 8: Cleanup script for old Virtual Hosts

Create and edit this file.
```bash
nano /etc/scripts/cleanup_vhosts.sh
```

File contents for `/etc/scripts/cleanup_vhosts.sh`.
```script
#!/bin/bash

# Only delete subzone configs older than 4 hours
find /etc/apache2/sites-available/ -type f -name "*subzone*.conf" -mmin +240 -exec rm -f {} \;
find /etc/apache2/sites-enabled/ -type f -name "*subzone*.conf" -mmin +240 -exec rm -f {} \;

# Reload Apache to apply changes
systemctl reload apache2
```

Set the correct file permissions.
```bash
chmod +x /etc/scripts/cleanup_vhosts.sh
```

Edit this file to create a `cron` job.
```bash
nano /etc/cron.d/http_cleanup
```

File contents for `/etc/cron.d/http_cleanup`.
```
0 * * * * /etc/scripts/cleanup_vhosts.sh
```

---