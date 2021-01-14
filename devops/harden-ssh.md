---
description: Lock down your remote access
---

# Harden SSH

> Tested on:  
> \* Arch Linux  
> \* Alpine Linux

## Copy Your Keys

Assuming you've already [created your SSH keys](https://help.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent), make sure you copy your SSH keys \(from a remote machine\) before proceeding.

```bash
ssh-copy-id <USER>@<HOST>[:<PORT>]
```

## Lock Down the Daemon

The following lines will disable settings in the `sshd` config file \(typically located @`/etc/ssh/sshd_config`\)

Don't allow anyone to login remotely with `root`. Ever.

```bash
sed -i 's/#\?\(PerminRootLogin\s*\).*$/\1 no/' /etc/ssh/sshd_config
```

If the user doesn't have a password.. they cant get in.

```bash
sed -i 's/#\?\(PermitEmptyPasswords\s*\).*$/\1 no/' /etc/ssh/sshd_config
```

Disable password logins. Keys only.

```bash
sed -i 's/#\?\(PasswordAuthentication\s*\).*$/\1 no/' /etc/ssh/sshd_config
```

Make sure to allow logins with public keys.

```bash
sed -i 's/#\?\(PubkeyAuthentication\s*\).*$/\1 yes/' /etc/ssh/sshd_config
```

Now restart `sshd` and see if you can login. 🤞



