## Description
### What are Locales ?

Locales determine how your system displays system language, date and time, number formats, and currency symbols.

In this lesson we are setting up a hybrid locale configuration:
- **System language:** English (US) so error messages and system output are in English
- **Regional formats:** Belgian (BE) so dates, numbers, and currency match Belgian standards

---

## Step 1: Check Current Locale Status

Display what's currently configured.
```bash
locale
```

**What this does:**
- **locale** = displays all current locale environment variables
- Shows which language and regional settings are active
- You are now using the default locale `"C"`

Output.
```
root@angelo:/# locale
LANG=C
LANGUAGE=
LC_CTYPE="C"
LC_NUMERIC="C"
LC_TIME="C"
LC_COLLATE="C"
LC_MONETARY="C"
LC_MESSAGES="C"
LC_PAPER="C"
LC_NAME="C"
LC_ADDRESS="C"
LC_TELEPHONE="C"
LC_MEASUREMENT="C" LC_IDENTIFICATION="C"
LC_ALL=
```

---

## Step 2: Check Available Locales

Display which locales are currently on your system.
```bash
locale -a
```

**What this does:**
- **locale -a** = list all available locales
- Shows which local combinations you can currently use
- These are the default locales as we have not configured any yet

Output. 
```
root@angelo:/# llocale -a
C
C.utf8
POSIX
```

---

## Step 3: Generate Required Locales

We need to generate two locales `en_US.UTF-8` and `nl_BE.UTF-8`. 
```bash
sudo dpkg-reconfigure locales
```

**What this does:**
- **sudo** = run this command with administrator privileges
- **dpkg-reconfigure** = reconfigure and already installed package
- **locales** = the package that manages system locales

**In the menu that appears:**
1. **Spacebar** to select: `en_US.UTF-8 UTF-8`
2. **Spacebar** to select: `n1_BE.UTF-8 UTF-8`
3. Press **Tab** to move to **"OK"**, then **Enter**
4. Choose `en_US.UTF-8` as the default locale
5. Press **Enter**

---

## Step 4: Check Available Locales Again

Display which locales are currently on your system.
```bash
locale -a
```

Output.
```
root@angelo:/# locale -a
C
C.utf8
POSIX
en_US.UTF-8
nl_BE.UTF-8
```

---

## Step 5: Configure Locale Settings

Now we need to set the proper locale configuration.
```bash
sudo update-locale LANG=en_US.UTF-8 LANGUAGE=en_US LC_NUMERIC=nl_BE.UTF-8 LC_TIME=nl_BE.UTF-8 LC_MONETARY=nl_BE.UTF-8 LC_PAPER=nl_BE.UTF-8 LC_NAME=nl_BE.UTF-8 LC_ADDRESS=nl_BE.UTF-8 LC_TELEPHONE=nl_BE.UTF-8 LC_MEASUREMENT=nl_BE.UTF-8 LC_IDENTIFICATION=nl_BE.UTF-8
```

**What this does:**
- **update-locale** = updates the `/etc/default/locale` file
- **LANG=en_US.UTF-8** = sets primary language to English (US)
- **LANGUAGE=en_US** = sets fallback language hierarchy
- **LC_NUMERIC** = sets the number format to 1.000,50
- **LC_TIME** = sets the date and time format to DD/MM/YYYY
- **LC_MONETARY** = sets currency format to €
- **LC_PAPER** = paper size (A4)
- **LC_NAME, LC_ADDRESS, LC_TELEPHONE** = sets name and address
- **LC_MEASUREMENT** = sets measurement system to metric
- **LC_IDENTIFICATION** = sets locale metadata

---

## Step 6: Verify Configuration File 

Check your configuration.
```bash
cat /etc/default/locale
```

**What this does:**
- **cat** = concatenate and display file contents
- Shows the locale configuration that was just get in step 5

---

## Step 7: Verify Current Locale Status

Log out of your server.
```bash
exit
```

Log back into your server.
```bash
ssh -p 22345 root@193.191.176.212
```

Display what you configured.
```bash
locale
```

Output.
```
root@angelo:/# locale
LANG=en_US. UTF-8
LANGUAGE=en_US
LC_CTYPE="en_US. UTF-8"
LC_NUMERIC=n1_BE.UTF-8
LC_TIME=n L_BE. UTF-8
LC_COLLATE="en_US. UTF-8"
LC_MONETARY=n 1_BE. UTF-8
LC_MESSAGES="en_US.UTF-8"
LC_PAPER=nL_BE- UTF-8
LC_NAME=n 1_BE. UTF-8
LC_ADDRESS=nL_BE.UTF-8
LC_TELEPHONE=n1_BE. UTF-8
LC_MEASUREMENT=nL_BE. UTF-8
LC_IDENTIFICATION=nL_BE. UTF-8
LC ALL=
```

---