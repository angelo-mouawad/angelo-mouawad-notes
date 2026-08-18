# Connecting Local Directory to Online Repository

These commands are used in Git Bash on Windows in order to connect a local directory to an online repository on `github.com`.

---

## Install And Configure Git

Install Git.
```bash
apt install git
```

If its the first time you use GitHub on your machine, you will have to setup your credentials.
```bash
git config --global user.name "yourGitHubUsername"
git config --global user.email "yourEmail@domain.com"
```

This will add your credentials to the `~/.ssh/config` we used in step 5.

---

## Generate An SSH Key Pair 

Generate a key pair. Create an ed25519 key pair on your local machine.
```bash
ssh-keygen
```

The previous command works fine as is. However for GitHub it is better to add some options.
```bash
ssh-keygen -t ed25519 -C "your_email@example.com" -f /home/user/.ssh/keyName
```

Commands options.
- `-t type` Specifies the key type like `rsa`, `ed25519`, or `ecdsa` (the default is `rsa`).
- `-b bits` Sets the key length in bits.
- `-C comment` Adds a comment to the key like your email for more security.
- `-f fileName` Specifies the output file path in case you want custom names.

File location: Your key pair will be saved in your SSH directory.
- The private key will be saved in `~/.ssh/keyName`.
- The public key will be saved in `~/.ssh/keyName.pub`.

Passphrase: You can optionally add a passphrase for extra protection.
- If you add one, you’ll need to type it when using the key (like a password for your key). 
- If you leave it blank by pressing Enter, you’ll be able to use the key without typing anything.

---

## Start The SSH Agent And Add Your Private Key (Optional)

If you're having problems with git recognizing your key on your machine. you can try to execute the following steps. Start the SSH agent.
```bash
eval "$(ssh-agent -s)"
```

Add your key to the agent.
```bash
ssh-add ~/.ssh/id_ed25519 
```

Confirm your key loaded.
```bash
ssh-add -l
```

The output of this command should show a fingerprint and your email

---

## Copy The Public Key And Add It To Your GitHub Account

Print the public key.
```bash
cat /home/user/.ssh/keyName.pub
```

Paste it into Github.
1. Select and copy the entire line which starts with `ssh-ed25519` and ends with your email.
2. In GitHub go to **Settings → SSH and GPG keys → New SSH key**.
3. Paste the key, give it a title, and the save.

---

## Make Sure GitHub Is A Known Host

Way 1 → Try connecting to github through SSH, this will automatically add github.com to the `known_hosts` file.
```bash
ssh -T git@github.com 
```

If prompted `Are you sure you want to continue connecting (yes/no)?` type `yes`.

Way 2 → Add GitHub directly to the `known_hosts` file.
```bash
ssh-keyscan github.com >> ~/.ssh/known_hosts
```

---

## Ensure SSH Uses The Right Key

Create and edit `~/.ssh/config` in order to set up GitHub configuration.
```
Host github.com
	HostName github.com
	User git
	IdentityFile ~/.ssh/KeyName
	IdentitiesOnly yes
```

This forces the use of that key for github.com and avoids offering many keys.

---

## Link Local Directory To Github Repository

Initialize git.
```bash
git init
```

Add SSH remote.
```bash
git remote add origin git@github.com:yourUsername/yourRepo.git
```

---

## Add Commit And Push

Create and edit the `.gitignore` file.
```bash
nano /etc/.gitignore
```

Add the files you don't want to be pushed to your online git repository.

Add your changes.
```bash
git add .gitignore hosts
```

Commit your changes.
```bash
git commit -m "Initial commit: added .gitignore and hosts"
```

Push your changes for the first time.  When pushing for the first time an upstream should be set. An upstream basically tells your local directory which branch it should push to.
```bash
git push -u origin master
```

Options for this command.
- `-u` = sets an upstream
- `--set-upstream` = also sets an upstream exactly like `-u`

Once you’ve done the first push, Git remembers your upstream for your local directory. So the next time you want to push you can simply just push without mentioning the upstream.
```bash
git push
```

---