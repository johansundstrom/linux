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

Setup linux user to Samba password

```sudo smbpasswd -a username```

## File permissions

In Linux, everything is a file. Directories are files, devives are files. 
Only Super user or "root" can access any file.

| Permission | Action | chmod option |
| --- | --- | --- |
| read | (view) | r or 4 |
| write | (edit) | w or 2 |
| execute | (execute) | x or 1 |

| User | ls output |
| --- | --- |
| owner | -rwx------ |
| group | ----rwx--- |
| other | -------rwx |




## Install Plex

- Download ```.deb``` package
- Run ```sudo dpkg -i "deb-package```
- Go to ```http://127.0.0.1:32400/web```
