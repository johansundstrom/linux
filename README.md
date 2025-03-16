# Linux 


## Install Samba

- ```sudo apt update```
- ```sudo apt install samba```

Create folder to share

```mkdir /srv/samba/share/```

Configuration

```sudo nano /etc/samba/smb.conf```

At the bottom of the file, add the following lines:

```
[sambashare]
comment = Samba on Ubuntu
path = /srv/samba/share
read only = no
browsable = yes
```

Restart service

```sudo service smbd restart```

Update firewall

```sudo ufw allow samba```

## Install Plex

- Download ```.deb``` package
- Run ```sudo dpkg -i "deb-package```
- Go to ```http://127.0.0.1:32400/web```
