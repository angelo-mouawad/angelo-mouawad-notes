# What Is DNS ?

**DNS** which stands for Domain Name System is basically the phonebook of the internet. Its job is to translate **human–friendly domain names** like `google.com` into **IP addresses** like `142.250.190.78` that computers use to communicate. Without DNS, you would have to memorize IP addresses instead of names.

In this lesson, you will learn how to set up and manage a **DNS server**. Specifically you will install BIND9 and make it an authoritative DNS server. You will install BIND9 and configure it so **your server controls your own DNS domain**.
- **BIND9** is a popular DNS server program. It answers DNS questions from other computers.
- **Authoritative DNS server** means your server is the official source of truth for your domain. 
- For example, if someone asks “What is the IP of `www.your-domain.com`?”, your server provides the official answer.

First you will create DNS records for your domain. A **DNS record** is a rule that connects a name to something (usually an IP address). You need to create these records.
- `ns.angelo-mouawad.sasm.uclllabs.be` Points to **your server’s IP**. This defines your DNS nameserver.
- `www.angelo-mouawad.sasm.uclllabs.be` Points to **your server’s IP**. This is your website address.
- `test.angelo-mouawad.sasm.uclllabs.be` Points to **193.191.177.254**. A test record pointing to a fixed IP.

Then you will **configure three nameservers (NS servers)**. Nameservers hold the DNS zone for your domain.  A **zone** is a file containing all DNS records for your domain. You must have **three NS servers**.
- **Master server**: `ns.angelo-mouawad.sasm.uclllabs.be` This is your own BIND9 server. The master contains the original zone file.
- **Slave server 1**: `ns1.uclllabs.be`
- **Slave server 2**: `ns2.uclllabs.be`

**Slaves also called secondary servers or backup servers** receive a copy of your DNS zone from the master. This ensures your DNS still works if one server goes down.

After that you will **allow zone transfers (AXFR) to slave1 and slave2**. A **zone transfer (AXFR)** is how a slave DNS server copies the zone from the master. You must configure your server so both `ns1.uclllabs.be` and `ns2.uclllabs.be` are allowed to download the zone file. Without zone transfers, the slaves won’t have your DNS data.

The you will **check with `dig` that SOA serials match on all three servers**. Dig is a command line tool used to query DNS servers.
- The **SOA record** stands for "Start of Authority", it contains administrative info about your DNS zone.
- The **Serial number** is a number inside the SOA record that changes whenever the zone file is updated.

All three servers, the master and the two slaves, must have the **same serial number**, which proves that the slaves updated correctly and the zone transfers worked.

For the last step **you must write two scripts** and put them in `/etc/scripts/`, the two scripts are:
- **`dns_add_zone`** This script creates a new DNS zone on the server.
- **`dns_add_record`** This script adds a new DNS record inside a zone.

Both scripts must have the correct file permissions, usually executable `chmod +x`.

**Finally you should implement cleanup to remove old subzones older than 4 hours.** Some tasks in your lab create temporary DNS zones which are called subzones. You must make a script that finds subzones older than **4 hours** and deletes them automatically. This keeps your DNS tidy and prevents old test data from piling up.

---

# What Is DIG ?

The `dig` command in Linux is a **DNS lookup tool**. It stands for **Domain Information Groper**, and it’s used to query DNS servers and retrieve information such as IP addresses, DNS records, and name server details.

Look up the IP address of a domain.  
```
dig example.com
```

By default, `dig` returns the **A record**, which is the IPv4 address of the domain.

Get specific DNS records like A, AAAA, MX, TXT, or NS.
```
dig example.com MX
```

Query a specific DNS server.
```
dig @8.8.8.8 example.com
```

The `@8.8.8.8` tells `dig` to **ask Google’s DNS server** (8.8.8.8) instead of your default system DNS.

Perform reverse DNS lookups.
```
dig -x 8.8.8.8
```

The `-x` performs **reverse DNS**, which means: “Given an IP address, tell me the domain name associated with it.”

---

## Step 1: Install Required Packages 

Required packages.
```bash
apt update
apt install -y bind9 bind9utils bind9-doc dnsutils
```

Make sure that your hostname is correctly setup.
```bahs
nano /etc/hostname
```

**What this does:**
- **nano** = creates file and then opens it in nano editor

File Contents for `/etc/hostname`.
```
angelo-mouawad
```

Make sure your hosts file is correctly setup.
```bash
nano /etc/hosts
```

File contents for `/etc/hosts`.
```
127.0.0.1       localhost
::1             localhost localhost.localnet

193.191.176.212 angelo-mouawad.sasm.uclllabs.be angelo-mouawad
```

Run this command.
```bash
hostnamectl set-hostname angelo-mouawad
```

Prepare directories and files>
```bash
mkdir -p /etc/bind/zones
touch /etc/bind/named.conf.yoda-zones
```

**What this does:**
- mkdir** = make directory
- **touch** = make file

---

## Step 2: Bind Configuration

Edit this file.
```bash
nano /etc/bind/named.conf.options
```

File contents for `/etc/bind/named.conf.options`.
```script
options {
	directory "/var/cache/bind";
	recursion no; // This server is NOT a resolver
	allow-query { any; }; // Allow queries from anywhere for our zones
	allow-transfer { none; }; // Default: no AXFR, override per-zone
	listen-on { any; }; // IPv4 only
	listen-on-v6 { none; };
	dnssec-validation auto;
	auth-nxdomain no;
};
```

Edit this file.
```
nano /etc/bind/named.conf.local
```

File contents for `/etc/bind/named.conf.local`.
```script
zone "angelo-mouawad.sasm.uclllabs.be" {
	type master;
	file "/etc/bind/zones/db.angelo-mouawad.sasm.uclllabs.be";
	
	// UCLL slaves allowed to AXFR your zone
	allow-transfer {
		193.191.177.20; // ns1.uclllabs.be
		193.191.177.21; // ns2.uclllabs.be
	};
	notify yes;
};
include "/etc/bind/named.conf.yoda-zones";
```

Edit your zone file.
```
nano /etc/bind/zones/db.angelo-mouawad.sasm.uclllabs.be
```

File contents.
```script
; Zone file for angelo-mouawad.sasm.uclllabs.be

$TTL 3600
@       IN  SOA  ns.angelo-mouawad.sasm.uclllabs.be. admin.angelo-mouawad.sasm.uclllabs.be. (
2025112926 ; serial (YYYYMMDDNN) - bump on each change
        3600        ; Refresh
        900         ; Retry
        1209600     ; Expire
        900         ; Negative Cache TTL
)

; Authoritative name servers
@       IN  NS  ns.angelo-mouawad.sasm.uclllabs.be.
@       IN  NS  ns1.uclllabs.be.
@       IN  NS  ns2.uclllabs.be.

; A records
ns      IN  A   193.191.176.212
@       IN  A   193.191.176.212
www     IN  A   193.191.176.212
test    IN  A   193.191.177.254
www1    IN  A   193.191.176.212
www2    IN  A   193.191.176.212
```

Restart bind.
```bash
sudo systemctl restart bind9
```

Test and verify.
```bash
dig -4 -t soa angelo-mouawad.sasm.uclllabs.be @127.0.0.1 +nocmd +noall + answer
dig -4 +short -t ns angelo-mouawad.sasm.uclllabs.be @127.0.0.1
```

**What this does:**
- **dig** = domain information groper, used to query `dns` servers 

---

## Step 3: Script Setup for Add Zone 

Setup the directory and permissions.
```bash
mkdir -p /etc/scripts
chown root:root /etc/scripts
chmod 750 /etc/scripts
```

**What this does:**
- **chmod** = changes who can read, write, or execute a file
- **chown** = changes owner of the file 

Create and edit this file.
```bash
nano /etc/scripts/dns_add_zone
```

File contents for `/etc/scripts/dns_add_zone`.
```script
#!/usr/bin/env bash
# dns_add_zone

set -euo pipefail

BASE="angelo-mouawad.sasm.uclllabs.be"
PARENT="/etc/bind/zones/db.${BASE}"
INCLUDE="/etc/bind/named.conf.yoda-zones"
ZONEDIR="/etc/bind/zones"
NS_FQDN="ns.${BASE}."
MY_IP="193.191.176.212"

# 1) Ensure script is run as root
if [ "$(id -u)" -ne 0 ]; then
  echo "Error: must be run as root (use sudo)"
  exit 1
fi

# 2) Expect exactly one argument: the subzone label
if [ "$#" -ne 1 ]; then
  echo "Usage: dns_add_zone <subzone-label>"
  exit 1
fi

LABEL="$1"

# 3) Validate label: lowercase letters, digits, dashes; no leading/trailing dash
if ! [[ "$LABEL" =~ ^[a-z0-9]([a-z0-9-]*[a-z0-9])?$ ]]; then
  echo "Error: invalid subzone label"
  exit 1
fi

FULL="${LABEL}.${BASE}"
ZONEFILE="${ZONEDIR}/db.${FULL}"

# 4) Do not override existing zone
if [ -e "$ZONEFILE" ]; then
  echo "Error: zone already exists: $FULL"
  exit 1
fi

mkdir -p "$ZONEDIR"

# Freeze parent zone if dynamic (ACME made it journaled)
rndc freeze "$BASE" >/dev/null 2>&1 || true

# 5) Create basic zone file: SOA + NS + A(@)
SERIAL="$(date +%Y%m%d)01"
cat >"$ZONEFILE" <<Z
\$TTL 600
@ IN SOA ns.${BASE}. admin.${BASE}. (
        ${SERIAL}  ; serial
        600 300 3600000 300 )
@ IN NS ns.${BASE}.
@ IN A  ${MY_IP}
Z

# 6) Add zone{} block to named.conf.yoda-zones (if not already there)
if ! grep -qE "^zone \"${FULL}\"" "$INCLUDE" 2>/dev/null; then
  cat >>"$INCLUDE" <<E
zone "${FULL}" {
    type master;
    file "/etc/bind/zones/db.${FULL}";
    allow-transfer { 193.191.177.20; 193.191.177.21; };
    notify yes;
};
E
fi

# 7) Add delegation in parent zone (LABEL IN NS ns.BASE.)
if ! grep -qE "^${LABEL}[[:space:]]+IN[[:space:]]+NS[[:space:]]+${NS_FQDN}\.?$" "$PARENT"; then
  echo -e "${LABEL}\tIN\tNS\t${NS_FQDN}" >> "$PARENT"
fi

# 8) Bump parent SOA serial (YYYYMMDDNN)
CUR=$(awk '/serial/ {print $1; exit}' "$PARENT" | tr -d '();')
TODAY=$(date +%Y%m%d)
if [[ "$CUR" =~ ^([0-9]{8})([0-9]{2})$ && "${BASH_REMATCH[1]}" == "$TODAY" ]]; then
  NEW="${TODAY}$(printf "%02d" $((10#${BASH_REMATCH[2]} + 1)))"
else
  NEW="${TODAY}01"
fi

sed -i "0,/^[[:space:]]*[0-9]\+[[:space:]]*;[[:space:]]*serial/s//${NEW} ; serial/" "$PARENT"

# 9) Validate and thaw+reload BIND
named-checkzone "$FULL" "$ZONEFILE"  >/dev/null
named-checkzone "$BASE" "$PARENT"    >/dev/null

rndc thaw "$BASE" >/dev/null 2>&1 || true
systemctl reload bind9 >/dev/null 2>&1 || systemctl restart bind9 >/dev/null 2>&1

echo "OK: created zone $FULL"
```

Set the correct permissions.
```bash
chmod 700 /etc/scripts/dns_add_zone
chown root:root /etc/scripts/dns_add_zone
```

---

## Step 4: Script Setup for Add Record 

Create and edit this file.
```bash
nano /etc/scripts/dns_add_record
```

File contents for `/etc/scripts/dns_add_record`.
```script
#!/usr/bin/env bash
# dns_add_record

set -euo pipefail

BASE_DOMAIN="angelo-mouawad.sasm.uclllabs.be"

# 1) Require root (Yoda calls via: su - check -c "sudo -n dns_add_record ...")
if [ "$(id -u)" -ne 0 ]; then
  echo "Error: This script must be run as root."
  exit 1
fi

TYPE="A"

# 2) Parse optional -t flag
while getopts ":t:" opt; do
  case "$opt" in
    t) TYPE="${OPTARG^^}" ;;  # uppercase
    *) echo "Usage: $0 [-t A|CNAME|MX] <name> <data> <zone>"; exit 1 ;;
  esac
done
shift $((OPTIND - 1))

# 3) Need: name, data, zone
if [ "$#" -lt 3 ]; then
  echo "Usage: $0 [-t A|CNAME|MX] <name> <data> <zone>"
  exit 1
fi

NAME="$1"
DATA="$2"
ZONE="${3%.}"                                  # strip trailing dot if any
ZONEFILE="/etc/bind/zones/db.${ZONE}"

# 4) Ensure zone file exists
if [ ! -f "$ZONEFILE" ]; then
  echo "Error: Zone file $ZONEFILE does not exist."
  exit 1
fi

# Is this the dynamic base zone?
IS_BASE=0
if [ "$ZONE" = "$BASE_DOMAIN" ]; then
  IS_BASE=1
fi

# 5) For dynamic base zone, freeze before editing
if [ "$IS_BASE" -eq 1 ]; then
  rndc freeze "$ZONE" >/dev/null 2>&1 || true
fi

append() { echo "$1" >> "$ZONEFILE"; }

case "$TYPE" in
  A)
    if ! [[ "$DATA" =~ ^([0-9]{1,3}\.){3}[0-9]{1,3}$ ]]; then
      echo "Error: invalid IPv4 address '$DATA'"
      [ "$IS_BASE" -eq 1 ] && rndc thaw "$ZONE" >/dev/null 2>&1 || true
      exit 1
    fi
    append "${NAME} IN A ${DATA}"
    ;;
  CNAME)
    TARGET="${DATA%.}."
    append "${NAME} IN CNAME ${TARGET}"
    ;;
  MX)
    if ! [[ "$DATA" =~ ^([0-9]{1,3}\.){3}[0-9]{1,3}$ ]]; then
      echo "Error: MX requires IPv4 for mail host A record"
      [ "$IS_BASE" -eq 1 ] && rndc thaw "$ZONE" >/dev/null 2>&1 || true
      exit 1
    fi
    append "@ IN MX 10 ${NAME}.${ZONE}."
    append "${NAME} IN A ${DATA}"
    ;;
  *)
    echo "Error: unsupported type '$TYPE' (use A, CNAME, or MX)"
    [ "$IS_BASE" -eq 1 ] && rndc thaw "$ZONE" >/dev/null 2>&1 || true
    exit 1
    ;;
esac

# 6) Bump SOA serial
CUR=$(awk '/serial/ {print $1; exit}' "$ZONEFILE" | tr -d '();')
TODAY=$(date +%Y%m%d)

if [[ "$CUR" =~ ^([0-9]{8})([0-9]{2})$ && "${BASH_REMATCH[1]}" == "$TODAY" ]]; then
  NEW="${TODAY}$(printf "%02d" $((10#${BASH_REMATCH[2]} + 1)))"
else
  NEW="${TODAY}01"
fi

sed -i "0,/^[[:space:]]*[0-9]\+[[:space:]]*;[[:space:]]*serial/s//${NEW} ; serial/" "$ZONEFILE"

# 7) Validate zone
named-checkzone "$ZONE" "$ZONEFILE" >/dev/null

# 8) Apply to named
if [ "$IS_BASE" -eq 1 ]; then
  # Thaw writes back dynamic state & reloads the zone
  rndc thaw "$ZONE" >/dev/null 2>&1 || true
else
  # Normal static zone: just reload this zone
  rndc reload "$ZONE" >/dev/null 2>&1 || true
fi

echo "OK: added ${TYPE} record in ${ZONE}"
```

Set the correct permissions.
```bash
chmod 700 /etc/scripts/dns_add_record
chown root:root /etc/scripts/dns_add_record
```

---

## Step 5: Script Setup for Cleanup 

Create and edit this file.
```bash
nano /etc/scripts/dns_cleanup
```

File contents for `/etc/scripts/dns_cleanup`.
```script
#!/usr/bin/env bash
# dns_cleanup

set -euo pipefail

BASE="angelo-mouawad.sasm.uclllabs.be"
PARENT="/etc/bind/zones/db.${BASE}"
INCLUDE="/etc/bind/named.conf.yoda-zones"
ZONEDIR="/etc/bind/zones"
NS_FQDN="ns.${BASE}."
MAX_AGE=$((4 * 3600))   # 4 hours in seconds

# 1) Require root
if [ "$(id -u)" -ne 0 ]; then
  echo "Error: must be run as root (use sudo)"
  exit 1
fi

now=$(date +%s)
changed=0

# 2) Freeze parent zone if it's dynamic (best-effort)
rndc freeze "$BASE" >/dev/null 2>&1 || true

# 3) Loop over all db.*.BASE zone files
for ZF in "$ZONEDIR"/db.*."$BASE"; do
  [ -f "$ZF" ] || continue

  zone=$(basename "$ZF" | cut -c4-)   # strip 'db.'
  [ "$zone" = "$BASE" ] && continue   # skip parent zone

  mtime=$(stat -c %Y "$ZF")
  age=$((now - mtime))

  if [ "$age" -gt "$MAX_AGE" ]; then
    echo "Cleaning expired subzone: $zone"
    label="${zone%.$BASE}"

    # (a) Remove delegation line in parent:
    #     <label> IN NS ns.BASE.
    sed -i "/^${label}[[:space:]]\+IN[[:space:]]\+NS[[:space:]]\+${NS_FQDN//./\\.}/d" "$PARENT"

    # (b) Remove its zone{} block from named.conf.yoda-zones
    awk -v target="$zone" '
      BEGIN { in_block=0 }
      # start of target zone block
      $0 ~ "^zone \""target"\"" {
          in_block=1
          next
      }
      # end of target zone block
      in_block && /^\};/ {
          in_block=0
          next
      }
      # skip any line while inside target block
      in_block { next }
      # keep all other lines
      { print }
    ' "$INCLUDE" > "${INCLUDE}.tmp" && mv "${INCLUDE}.tmp" "$INCLUDE"

    # (c) Delete the zone file and any journal
    rm -f "$ZF" "$ZF.jnl"

    # (d) Flush any cached data for that subzone (best-effort)
    rndc flushname "$zone" >/dev/null 2>&1 || true
    rndc flushname "${label}.${BASE}" >/dev/null 2>&1 || true

    changed=1
  fi
done

# 4) If we removed something, update parent serial + reload parent zone
if [ "$changed" -eq 1 ]; then
  CUR=$(awk '/serial/ {print $1; exit}' "$PARENT" | tr -d '();')
  TODAY=$(date +%Y%m%d)

  if [[ "$CUR" =~ ^([0-9]{8})([0-9]{2})$ && "${BASH_REMATCH[1]}" == "$TODAY" ]]; then
    NEW="${TODAY}$(printf "%02d" $((10#${BASH_REMATCH[2]} + 1)))"
  else
    NEW="${TODAY}01"
  fi

  sed -i "0,/^[[:space:]]*[0-9]\+[[:space:]]*;[[:space:]]*serial/s//${NEW} ; serial/" "$PARENT"

  # Sanity-check parent
  named-checkzone "$BASE" "$PARENT" >/dev/null || {
    echo "Warning: named-checkzone failed for $BASE" >&2
  }

  # Thaw + reload parent zone so BIND uses updated file instead of stale journal
  rndc thaw "$BASE" >/dev/null 2>&1 || true
  rndc reload "$BASE" >/dev/null 2>&1 || systemctl reload bind9 >/dev/null 2>&1 || systemctl restart bind9 >/dev/null 2>&1

  echo "Cleanup complete: old subzones removed."
else
  # No changes: just thaw if we froze
  rndc thaw "$BASE" >/dev/null 2>&1 || true
  echo "Nothing to cleanup."
fi
```

Set the correct permissions.
```bash
chmod 700 /etc/scripts/dns_cleanup
chown root:root /etc/scripts/dns_cleanup
```

Edit this file to create a `cron` job.
```bash
nano /etc/cron.d/dns_cleanup
```

File contents for `/etc/cron.d/dns_cleanup`.
```
0 * * * * root /etc/scripts/dns_cleanup
```

---

### Step 6: Allow the user check to run the script

Edit and create this file.
```bash
nano /etc/sudoers.d/dns-lab
```

File contents for `/etc/sudoers.d/dns-lab`.
```file
Defaults secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin:/etc/scripts"

Cmnd_Alias DNS_LAB_CMDS = \
    /etc/scripts/dns_add_zone, \
    /etc/scripts/dns_add_record

check ALL=(root) NOPASSWD: DNS_LAB_CMDS
```

Set the correct permissions.
```bash
chown root:root /etc/sudoers.d/dns-lab
chmod 440 /etc/sudoers.d/dns-lab
```

Test and verify.
```bash
sudo -u check sudo /etc/scripts/http_add_vhost test.angelo-mouawad.sasm.uclllabs.be
```

---