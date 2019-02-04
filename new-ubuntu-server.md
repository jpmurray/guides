# Setup a new Ubuntu server

1. Create a new user
And give it sudo rights.

```bash
adduser #username#
usermod -aG sudo #username#
```

2. Setup Firewall

```bash
apt install ufw # if needed
ufw allow OpenSSH
```

3. Copy SSH keys to new user and disable password auth

```bash
ssh-copy-id #username#@#host#
sudo nano /etc/ssh/sshd_config
# find PasswordAuthentication and set it to no
sudo systemctl restart ssh
```

4. Make sure unnatended upgrades is setup
```bash
sudo apt install unattended-upgrades
```