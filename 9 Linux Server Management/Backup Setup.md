# What are Backups ?

A **backup** is a **copy of data** that is created and stored separately from the original files, so that the information can be **restored** in case the original data is lost, damaged, or corrupted.

The goal of this lesson is to learn how to protect server configuration files against loss or accidental changes by setting up automated backup mechanisms.  

By the end of the lesson, we will have 2 different types of backups:
  - **Automatic on-change backups**: triggered immediately when a configuration file changes
  - **Scheduled clean-up and maintenance**: to prevent unnecessary clutter in `/etc/yoda`

These techniques ensure that if something goes wrong (like a system misconfiguration, human error, or crash), we can **restore** the system to a previous known-good state.

In this lesson we will also learn what `cron` jobs are, a `cron` job is a scheduled task on a Linux system. It allows you to **run scripts or commands automatically at specified times or intervals**. Cron jobs are managed by the **cron daemon**, a background service that runs continuously.

There are two ways to setup a `cron` job. The first way is by using `crontab`, `crontab` lets a **specific user create their own cron jobs**. You don’t need to specify the username because it automatically runs as the user who created it.
```bash
crontab -e
```

Format.
```
0 2 * * * /home/user/cleanup.sh
```

The second way to setup a `cron` job is using `/etc/cron.d/`, you create a **file inside `/etc/cron.d/`** for your job. This is **system-wide**, meaning it can be used for tasks that need specific users or affect multiple users. Format of the file is like a normal cron entry **but must include the username** to run the command as.
```bash
nano /etc/cron.d/filename
```

Format.
```
0 2 * * * root /usr/local/bin/cleanup.sh
```

---

## Step 1: Create the on-change Backup Script

First we create the script directory.
```bash
mkdir -p /etc/scripts
```

**What this does:**
- **mkdir** = make directory
- **-p** = makes sure parents directory in the path are also created

Then we create the script file and edit it.
```bash
nano /etc/scripts/backup.sh
```

**What this does:**
- **nano** = creates file and then opens it in nano editor

File contents for `/etc/scripts/backup.sh`.
```script
#!/bin/bash

set -euo pipefail

TARGET="$1"

# ignore vim swap/temp files
if echo "$TARGET" | grep -qP "\..*\.sw(p|x|px)$" ; then
    exit 0
fi

# create backup root
mkdir -p /var/backups/inotify

# copy preserving parent dirs; handle spaces
cp -p --parents "$TARGET" /var/backups/inotify

# move to timestamped name
TS="$(date +'%Y.%m.%d_%H:%M:%S')"
mv "/var/backups/inotify${TARGET}" "/var/backups/inotify${TARGET}_${TS}" 2>/dev/null || true

# set sane ownership/permissions
chown -R root:root /var/backups/inotify || true

```

Set the correct file permissions and make it executable.
```bash
chmod 755 /etc/scripts/backup.sh
chown root:root /etc/scripts/backup.sh

```

**What this does:**
- **chmod** = changes who can read, write, or execute a file
- **chown** = changes owner of the file 

---

## Step 2: Create the Script for incron Configuration

This script will enumerate directories under `/etc` (excluding `.git` and incron.d) and write an `incron` config file `/etc/incron.d/etc.conf`. It should be run periodically (we’ll schedule it later).

Edit this file.
```bash
nano /etc/scripts/incron_config.sh
```

File contents for `/etc/scripts/incron_config.sh`.
```script
#!/bin/bash
# /etc/scripts/incron_config.sh
# Regenerate /etc/incron.d/etc.conf to watch /etc directory tree.

set -euo pipefail

OUTFILE="/etc/incron.d/etc.conf"
TMP="$(mktemp)"
# find directories under /etc but exclude .git and incron.d itself
# For each directory we create a line:
# <dir> IN_CLOSE_WRITE,recursive=false /etc/scripts/backup.sh $@/$#
# Use literal $@ and $# in the file so incron expands them.
find /etc -type d ! -regex '.*/\.git.*' ! -path '/etc/incron.d' -print0 \
  | xargs -0 -I{} printf '%s IN_CLOSE_WRITE,recursive=false /etc/scripts/backup.sh $@/$#\n' "{}" > "${TMP}"
mv "${TMP}" "${OUTFILE}"
chmod 644 "${OUTFILE}"
# restart incron to pick up new config
systemctl restart incron || true

```

Set the correct file permissions and make it executable.
```bash
chmod 755 /etc/scripts/incron_config.sh
chown root:root /etc/scripts/incron_config.sh
```

---

## Step 3: Verify incron is Installed and Enabled

Check if incron is installed and enabled, then create initial incron config.
```bash
apt update
apt install -y incron
```

Allow system incron to run (it normally runs as root).
```bash
systemctl enable --now incron
```

Run the script once to generate config immediately
```bash
/etc/scripts/incron_config.sh
```

Check status and logs.
```bash
systemctl status incron --no-pager
journalctl -u incron -n 200 --no-pager
```

**What this does:**
- **systemctl status** = shows the current status of the service, displays running, stopped, or failed
- You should see that `incron` is active (running)
- You should also see `incron` logs

---
## Step 4: Schedule incron configuration to Run

Create and edit this cron file to regenerate incron config for /etc every 15 minutes
```bash
nano /etc/cron.d/incron-refresh
```

File contents for `/etc/cron.d/incron-refresh`.
```
*/15 * * * * root /etc/scripts/incron_config.sh >/dev/null 2>&1
```

Set file permissions.
```bash
chmod 644 /etc/cron.d/incron-refresh
```

---

## Step 5: Test the on-change backup flow

Create a test file in `/etc`.
```bash
echo "hello world" > /etc/test_inotify_file
```

**What this does:**
- **echo** = writes content to a file
- **>** = redirects output into a file, overwriting it

Wait a moment and list backups.
```bash
ls -l /var/backups/inotify/etc/test_inotify_file*
```

You should see a timestamped copy that now acts as the backup to the selection you made.

---

## Step 6: Cleaning up yoda File

Create a cron job to remove items in `/etc/yoda` older than 240 minutes (4 hours).

Create and edit this file.
```bash
nano /etc/scripts/cleanup_yoda.sh
```

File contents for `/etc/scripts/cleanup-yoda.sh`
```script
#!/bin/bash
# /etc/scripts/cleanup_yoda.sh
# Remove items in /etc/yoda older than 240 minutes (4 hours)

set -euo pipefail
YDIR="/etc/yoda"
if [ -d "${YDIR}" ]; then
  find "${YDIR}" -mindepth 1 -maxdepth 1 -mmin +240 -exec rm -rf {} \;
fi
```

Set the correct file permissions and make it executable.
```bash
chmod 755 /etc/scripts/cleanup_yoda.sh
chown root:root /etc/scripts/cleanup_yoda.sh
```

Add a cron file to run every hour.
```bash
nano /etc/cron.d/cleanup_yoda
```

File contents for `/etc/cron.d/cleanup-yoda`.
```bash
# Run cleanup-yoda every hour
0 * * * * root /etc/scripts/cleanup_yoda.sh >/dev/null 2>&1
```

Set the correct file permissions.
```bash
chmod 644 /etc/cron.d/cleanup_yoda
```

---

## Step 7: Verify Everything Works

Create or modify a file under `/etc`.
```bash
echo "hello world" > /etc/test.conf
```

Check `/var/backups/inotify`.
```bash
ls -l /var/backups/inotify/etc/test.conf*
```
**
You should see a timestamped copy that now acts as the backup to the selection you made.

---