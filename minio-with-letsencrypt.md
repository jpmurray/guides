# Minio Installation Guide

Made this so I don't lose my way into setting up new instances of Minio with Let's Encrypt. My first experience was not good (probably because of late night coding), so out of being afraid to repeat the same error, here are my steps.

1. Set the server DNS
2. Set up Ubuntu
3. Create a user for minio: `sudo useradd --system minio-user --shell /sbin/nologin`
4. Download latest build, move it to a decent location and set permission / ownership: `curl -O https://dl.minio.io/server/minio/release/linux-amd64/minio && sudo mv minio /usr/local/bin && sudo chmod +x /usr/local/bin/minio && sudo chown minio-user:minio-user /usr/local/bin/minio`
5. Configure Minio
`/user/local/share/minio` is the volume for the actual data

```bash
# Create directories and sets permissions / ownership
sudo mkdir /usr/local/share/minio && sudo mkdir /etc/minio && sudo mkdir /etc/minio/certs && sudo chown minio-user:minio-user /usr/local/share/minio && sudo chown minio-user:minio-user /etc/minio && sudo chown minio-user:minio-user /etc/minio/certs
```

```bash
# create a /etc/default/minio and add this content to it (check for hostname to change):
MINIO_VOLUMES="/usr/local/share/minio/"
MINIO_OPTS="-C /etc/minio --address {hostname}:443"
```

Make sure `443` is usable and opened:
```bash
sudo setcap 'cap_net_bind_service=+ep' /usr/local/bin/minio
sudo ufw allow 443 && sudo ufw reload
```

6. Configure minio as a service
```bash
curl -O https://raw.githubusercontent.com/minio/minio-service/master/linux-systemd/minio.service && sudo mv minio.service /etc/systemd/system
sudo systemctl daemon-reload && sudo systemctl enable minio
```

7. Configure Let's encrypt with Certbot
```bash
sudo apt update && sudo apt install software-properties-common
sudo add-apt-repository ppa:certbot/certbot && sudo apt update
sudo apt install certbot
# Watch for hostname and email value to change in the next command
sudo ufw allow 80 && sudo ufw reload
sudo certbot certonly --standalone -d {hostname} --staple-ocsp -m {email} --agree-tos
sudo ufw deny 80 && sudo ufw reload
sudo cp /etc/letsencrypt/live/{hostname}/fullchain.pem /etc/minio/certs/public.crt && sudo cp /etc/letsencrypt/live/{hostname}/privkey.pem /etc/minio/certs/private.key
sudo chown minio-user:minio-user /etc/minio/certs/public.crt && sudo chown minio-user:minio-user /etc/minio/certs/private.key
```

8. Start Minio
Star it :`sudo service minio start`, and check if everything is ok: `sudo service minio start`.

Configuration (and access + secret key) can be found in `/usr/local/share/minio/.minio.sys/config/config.json`

## Missing
I have to make steps to renew the certificate by itself. Currenly easier in root since sudoing in a script isn't easy. All in all, what is needed to renew is:

1. Enable port : `sudo ufw allow 80 && sudo ufw reload`
2. Run command : `sudo certbot certonly --standalone -d {hostname} --staple-ocsp -m {email} --agree-tos`
3. Reblock port: `sudo ufw deny 80 && sudo ufw reload`

If it's not working, renewal might try to do its thing over port 443, which Minio use. If it's the case, stop and restart minio.

