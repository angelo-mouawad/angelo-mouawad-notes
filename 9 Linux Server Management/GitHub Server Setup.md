# What is GitHub ?

**GitHub** is an online platform for storing, sharing, and collaborating on code projects. It’s built around **Git**, a version control system that tracks changes in files, especially useful for software development.

The goal of this lesson is to learn how to link your local server directory to your online git repository using SSH key pairs. SSH key pairs provide way more safety than just using a password. After GitHub's new update, passwords are no longer accepted, so this link can only be achieved through SSH key pairs.

---

## Step 1: Install and Configure Git

Install Git.
```bash
apt install git -y
```

Configure Git.
```bash
git config --global user.name "YourGitHubUsername"
git config --global user.email "your-email@domain.com"
```

You can now check your configuration by using this command.
```bash
git config --global --list
```

---

## Step 2: Generate SSH Key and Connect

Generate a new key pair, if not already created. Do not set a passphrase.
```bash
ssh-keygen -t ed25519 -C "your-email@domain.com"
```

This command creates a new SSH key pair (a public and private key) using the `ed25519` encryption algorithm. Both keys will be saved in the `/.ssh` directory unless you specify another directory.

Now copy the public key from the directory to put on GitHub. The private key stays on your server.
```bash
cat ~/.ssh/id_ed25519.pub
```

**What this does:**
- **cat** = display file contents
- **~** = your home directory `/home/angelo-mouawad`

**Add it to GitHub:**
1. Go to GitHub then settings
2. Press the SSH and GPG keys tab
3. Press New SSH key
4. Finally paste your public key and give it a name

---

## Step 3: Test the SSH Connection

Add GitHub to known hosts.
```bash
ssh-keyscan github.com >> ~/.ssh/known_hosts
```

Now test the SSH connection.
```bash
ssh -T git@github.com
```

Output.
```
root@angelo:/# ssh -T git@github.com
Hi ! You've successfully authenticated, but GitHub does not provide shell access.
```

Finally create a private GitHub repository. Make sure the repository is private otherwise people will have access to your files.

On GitHub:
1. Press New Repository
2. Give your repository a name like `sasm-lab`
3. Make sure your set the visibility to private
4. Don’t add a README or License

---

## Step 4: Link Local Directory to GitHub Repository

Initialize git.
```bash
cd /etc
git init
```

Add SSH remote.
```bash
git remote add origin git@github.com:angelo-mouawad/sasm-lab.git
```

Verify your remote was added by listing all remotes.
```bash
git remote -v
```

---

## Step 5: Configure gitignore

Edit the `.gitignore` file.
```bash
nano /etc/.gitignore
```

**What this does:**
- **nano** = creates file and then opens it in nano editor

File contents for `/etc/.gitignore`.
```
# System junk
*.swp
*.tmp
*.log

# Editor backups
*~

# Cache / runtime
_pycache_/
*.pid

# Server-specific stuff
yoda/
node_modules/
.env
incron.d/etc.conf
```

---

## Step 6: Add Commit Push

Add your changes.
```bash
git add .gitignore hosts
```

Commit your changes.
```bash
git commit -m "Initial commit: added .gitignore and hosts"
```

Push your changes.
```bash
git push -u origin master
```

---

