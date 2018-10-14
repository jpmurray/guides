# Minio Installation Guide

Made this so I don't lose my way into setting up new instances of Minio with Let's Encrypt. My first experience was not good (probably because of late night coding), so out of being afraid to repeat the same error, here are my steps.

- Set up Ubuntu (just check Digital Ocean if your need). Make your sudo user `minio-user`, since it's the decent default everywhere, and log in as it.
- Download the latest build, make it executable and move it to bin `curl -O https://dl.minio.io/server/minio/release/linux-amd64/minio && sudo chmod +x minio && sudo mv minio /usr/local/bin`.
- Create the directory `/home/minio-user/data` for good measure, that's where I put my data, since my home folder is part of a `raid` array.
- Enable port 443 in firewall: `sudo ufw enable 443 && sudo ufw reload`.
- Create the environment file with sudo (`sudo nano /etc/default/minio`) and add the following values:

```
MINIO_VOLUMES="/home/minio-user/data"
MINIO_OPTS="--address :443"
```

- Download the systemd script and activate it `curl -O https://raw.githubusercontent.com/minio/minio-service/master/linux-systemd/minio.service && sudo mv minio.service /etc/systemd/system && sudo systemctl daemon-reload && sudo systemctl enable minio`
- Install Certbot (`https://certbot.eff.org/`)
- Get certificate: `certbot certonly --standalone -d {domain} -m {email} --agree-tos --staple-ocsp`
- Copy the certificate to Minio's certs directory: `sudo cp /etc/letsencrypt/live/{domain}/fullchain.pem /home/minio-user/.minio/certs/public.crt && sudo cp /etc/letsencrypt/live/{domain}/privkey.pem /home/minio-user/.minio/certs/private.key`
- Start Minio `sudo systemctl start minio`

Configuration (and access + secret key) can be found in `/home/minio-user/data/.minio.sys/config/config.json`

## Missing
I have to make steps to renew the certificate by itself. Currenly easier in root since sudoing in a script isn't easy.
