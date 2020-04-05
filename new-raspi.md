# Raspi Setup

1. Install Raspbian.
2. sudo apt update && sudo apt upgrade && sudo apt dist-upgrade.
3. Mount NAS somewhere for backups.
	- Have some credential file somewhere `username=sambausername` and `password=sambapassword` on their own lines.
	- Add to `/etc/fstab` : `//IP_OF_NAS/Sharename  /Path/to/share/folder  cifs  vers=2.0,credentials=/path/to/.smbcredentials,iocharset=utf8,gid=1000,uid=1000,file_mode=0777,dir_mode=0777 0 0` and run `sudo chmod 600 /path/to/.smbcredentials`.
3. Install [Log2Ram](https://github.com/azlux/log2ram).
4. Install [ClonePi](https://github.com/SpoddyCoder/clonepi).
	- Run `sudo clonepi /path/to/shared/mount/name_backup.img.gz --init-destination`.
	- Run `sudo nano crontab -e` and add to run once a week:
	- `00 00 * * 0 /usr/local/sbin/clonepi /path/to/shared/mount/name_backup.img.gz --script --quiet >> /var/log/clonepi.log 2>&1`