# What is a Database ?

Database definition. A database is an organized collection of data. Data is stored in tables with rows and columns. Databases allow adding, reading, updating, and deleting data. Managed by a DBMS like MySQL or MariaDB. Queries (SQL) are used to access or manipulate the data. Ensures data is structured, consistent, and easily retrievable.

The goal of this lesson is to:
- Install and verify all required components, including MariaDB, PHP, a web server, and the `mysqli` PHP extension for database communication.
- Create a database named **`check`** and a table **`log`** with an auto-incrementing ID, timestamp, and a text field.
- Create a MySQL user **`check`** with a secure password and grant it only necessary permissions (INSERT and SELECT), while restricting host access for security.
- Create an additional user **`check@localhost`** to allow PHP on the server to authenticate properly.
- Populate the `log` table with **80–100 sample rows** containing unique text strings to simulate real log data.
- Create a PHP script that connects to the database, fetches the most recent log entry, and prints it.
- Place the PHP file in the web server’s root directory and set correct permissions so the server can access it.
- Test the setup using `curl` locally and via the server's public hostname to ensure the PHP script returns the newest log entry correctly.

---

## Step 1: Install and Configure Requirements

Display what's currently configured.
```bash
apt update
apt install -y mariadb-server php php-mysqli apache2
```

Start and enable services.
```bash
systemctl enable --now mariadb
```

Secure Maria Database.
```bash
mysql_secure_installation
```

---

## Step 2: Database Setup

Open a root MySQL shell.
```bash
mysql -u root
```

Run these statements inside the SQL shell.
```sql
-- create database (backticks because check is reserved)
CREATE DATABASE IF NOT EXISTS `check` CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;

USE `check`;

-- create table log
CREATE TABLE IF NOT EXISTS `log` (
  id INT AUTO_INCREMENT PRIMARY KEY,
  `date` DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `text` VARCHAR(50) NOT NULL
) ENGINE=InnoDB;

-- create the user 'check' allowed to connect from remote host (yoda IP)
CREATE USER IF NOT EXISTS 'check'@'193.191.177.12' IDENTIFIED BY 'rDEetGxq82DCE';

-- grant only necessary privileges (INSERT and SELECT) on database `check` for remote user
GRANT INSERT, SELECT ON `check`.* TO 'check'@'193.191.177.12';

-- create the user 'check' allowed to connect from localhost (for PHP scripts)
CREATE USER IF NOT EXISTS 'check'@'localhost' IDENTIFIED BY 'rDEetGxq82DCE';

-- grant only necessary privileges (INSERT and SELECT) on database `check` for localhost user
GRANT INSERT, SELECT ON `check`.* TO 'check'@'localhost';

-- apply changes
FLUSH PRIVILEGES;
```

---

## Step 3: Populate the Table with Sample Data

Run this as the Linux user not in the SQL shell.
```bash
# create 90 rows
for i in $(seq 1 90); do
  text=$(printf "entry-%03d" "$i")
  # Escape quotes if needed and ensure length <= 50
  sudo mysql -D'check' -e "INSERT INTO \`log\` (\`date\`, \`text\`) VALUES (NOW(), '${text}');"
  # sleep 1 sec to have different timestamps (optional)
  sleep 0.1
done
```

Verify count.
```bash
mysql -D'check' -e "SELECT COUNT(*) AS cnt FROM \`log\`;"
```

---

### Step 4: Create the PHP checker

Edit and create this file.
```bash
nano /var/www/html/mysql_check.php
```

**What this does:**
- **nano** = creates file and then opens it in nano editor

File contents for `/var/www/html/mysql_check.php`.
```php
<?php
// mysql_check.php
// Purpose: output the most recently inserted `text` from the `check`.`log` table.

// Database connection settings
$host = 'localhost';      // use localhost if the DB server is same machine; if remote change appropriately
$db   = 'check';
$user = 'check';
$pass = 'rDEetGxq82DCE';
$charset = 'utf8mb4';

// Create mysqli connection
$mysqli = new mysqli($host, $user, $pass, $db);

// Check connection
if ($mysqli->connect_errno) {
    // Do not reveal credentials; return 500-like output
    http_response_code(500);
    echo "DB_CONN_ERR";
    exit;
}

// Use a prepared statement to get the most recent text
$stmt = $mysqli->prepare("SELECT `text` FROM `log` ORDER BY `date` DESC, id DESC LIMIT 1");
if (!$stmt) {
    http_response_code(500);
    echo "DB_QUERY_ERR";
    exit;
}

$stmt->execute();
$stmt->bind_result($text);
if ($stmt->fetch()) {
    // Output only the string, no HTML, newline is okay for curl
    // Trim to 50 chars to be safe
    echo substr($text, 0, 50);
} else {
    // no rows
    echo "";
}

$stmt->close();
$mysqli->close();
?>

```

Set the correct permissions.
```bash
chown www-data:www-data /var/www/html/mysql_check.php
chmod 644 /var/www/html/mysql_check.php
```

**What this does:**
- **chmod** = changes who can read, write, or execute a file
- **chown** = changes owner of the file 

---

## Step 5: Make sure MySQL listens on network

Edit the SQL configuration file.
```bash
nano /etc/mysql/mariadb.conf.d/50-server.cnf
```

File contents for `/etc/mysql/mariadb.conf.d/50-server.cnf`.
```
bind-address = 0.0.0.0
```

Find this line and fix it.

Then restart Maria Database and Apache.
```bash
systemctl restart mariadb
systemctl restart apache2
```

---

## Step 6: Test from the server

On the server itself.
```bash
curl -4 http://localhost/mysql_check.php
```

Then test externally.
```bash
curl -4 http://angelo-mouawad.sasm.uclllabs.be/mysql_check.php
```

You should get one of the inserted `text` values.

---
