---
title: "Secure WSL2 Credentials with pass, GnuPG, and Kleopatra"
date: 2026-05-06T10:04:41-07:00
categories: ["blog", "credential management"]
weight: 5
---

Here is the complete Markdown article for your setup. It includes the updated
path logic and the switch to pipx for better tool isolation.

This guide walks you through a "hardened" credential setup: storing secrets in
the Linux-native pass utility within WSL2, while leveraging Gpg4win/Kleopatra on
Windows to handle secure graphical passphrase prompts.

## Why this setup?

* Safety: Secrets are encrypted at rest with GnuPG.
* Convenience: Uses the Windows GUI (Kleopatra) for passphrase entry.
* More robust than other pinentry setup, especially when you use pgp keys for
  different applications, such as magit on `emacs -nw` or `sbt publishSigned`,
  that asks passphrases in an async interactive session of a shell.

------------------------------

## 1. Prerequisites

   1. Windows: [Gpg4win](https://www.gpg4win.org/) installed (includes Kleopatra).
   2. WSL2: A Linux distro (e.g., Ubuntu) installed.

------------------------------
## 2. Generate Your GPG Key Pair

Before using pass, you need a GPG key pair to encrypt your secrets. Generate the key:

```bash
gpg --full-generate-key
```

- Select (1) RSA and RSA (or ECC if preferred).
- Choose a keysize (e.g., 4096).
- Set the expiration (e.g., 0 for no expiration).
- Enter your Name and GitHub Email Address.
- Find your Key ID:

```bash
gpg --list-secret-keys --keyid-format LONG
```

Look for the line starting with sec. The ID is the alphanumeric string after the slash (e.g., 3AA5C34371567BD2). Copy this ID.

------------------------------

## 3. Install and Initialize pass

[pass](https://www.passwordstore.org/) is the standard unix password manager.

   1. Install pass:
   
   ```bash
   sudo apt update && sudo apt install pass -y
   ```
   
   2. Initialize the Store: Use your GPG Key ID (find it with gpg --list-keys):
   
   ```bash
   pass init "YOUR_KEY_ID"
   ```
   
   3. Store your GitHub PAT:
   
   ```bash
   pass insert GitHub/token
   ```
   Paste your GitHub Personal Access Token(PAT) when prompted.
   You can also add multiple PATs for different repositories/organizations.
   
------------------------------
## 4. Install pass-git-helper via pipx
Using pipx ensures the tool is installed in an isolated environment.

   1. Install pipx:

   ```bash
   sudo apt update && sudo apt install pipx -y
   pipx ensurepath
   ```
   
   (Restart your terminal after this step to refresh your PATH)
   2. Install the helper:
   
   ```bash
   pipx install pass-git-helper
   ```
 
## 5. Link WSL2 to Windows Pinentry

Redirect the "Pinentry" (password entry) to the Windows executable to get GUI prompts.

   1. Identify the Path: On modern 64-bit systems, Gpg4win is located at:
   
   `C:\Program Files\GnuPG\bin\pinentry-basic.exe`
   
   2. Configure the Agent: Open (or create) the GPG agent config in WSL:
   
   ```bash
   vi ~/.gnupg/gpg-agent.conf
   ```
   
   3. Add the following line:
   
   `pinentry-program "/mnt/c/Program Files/GnuPG/bin/pinentry-basic.exe"`
   
   Note: Do not use a backslash before the space.

   4. Restart the Agent:
   
   ```bash
   gpgconf --kill gpg-agent
   ```

------------------------------
## 6. Configure Git to use pass

   1. Create the Mapping Config:
   
   ```bash
   mkdir -p ~/.config/pass-git-helper
   vi ~/.config/pass-git-helper/config.ini
   ```
   
   2. Map GitHub to your pass entry:
   
   ```
   [github.com]
   target=GitHub/token
   ```
   
   3. Update .gitconfig:
   
   ```bash
   git config --global credential.helper '!pass-git-helper $@'
   ```
   
   If you have to use different PAT for different repository access, configure
   it per repository in `~/.gitconfig` file with different server sections. You
   can find examples at [Git documentation](https://git-scm.com/docs/git-config#_example).

------------------------------
## 7. Environment Finalization
Add this to your ~/.bashrc or ~/.zshrc to ensure GPG communicates with your terminal session:

export GPG_TTY=$(tty)

Run `source ~/.bashrc` to apply.

------------------------------
## 8. The Test
Run a command that requires your token:

```bash
git ls-remote
```

A Kleopatra/Gpg4win window pops up on your Windows desktop. Enter your passphrase there, and your WSL terminal will proceed.

------------------------------
## Troubleshooting

* "No pinentry" error: Double-check that your `~/.gnupg/gpg-agent.conf` uses forward slashes(/) and points to the correct Program Files directory (64-bit).
* Path verification: Run `ls "/mnt/c/Program Files/GnuPG/bin/pinentry-basic.exe"` in WSL to ensure the path is reachable.

