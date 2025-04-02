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

Directories have Folder/directory permission

| Permission | Action | chmod option |
| --- | --- | --- |
| read | (view contents, i.e. ls command) | r or 4 |
| write | (create or remove files from dir) | w or 2 |
| execute | (cd into directory) | x or 1

källa https://help.ubuntu.com/community/FilePermissions

## Install Plex

- Download ```.deb``` package
- Run ```sudo dpkg -i "deb-package```
- Go to ```http://127.0.0.1:32400/web```

## Install BIND

Install BIND9

```sudo apt install bind9```

Edit the new zone file ```/etc/bind/db.example.com``` and change localhost. to the FQDN of your server, including the additional . at the end. 

```sudo nano /etc/bind/db.example.com```

```
;
; BIND data file for example.com
;
$TTL    604800
@       IN      SOA     example.com. root.example.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL

@       IN      NS      ns.example.com.
@       IN      A       192.168.1.10
@       IN      AAAA    ::1
ns      IN      A       192.168.1.10
```

Restart BIND9

```sudo systemctl restart bind9.service```


/etc/bind/named.conf.options: Global DNS options

/etc/bind/named.conf.local: For your zones

/etc/bind/named.conf.default-zones: Default zones such as localhost, its reverse, and the root hints


