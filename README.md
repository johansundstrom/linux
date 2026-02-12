# Linux 

## Innehåll

* Samba
* File permissions
* Permanent IP
* Plexmediaserver
* BIND9
* DHCP
* Montera nätverksenhet permanent

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

## Permanent IP

Identifiera Nätverksinterface

```ip a```

```yaml
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute
       valid_lft forever preferred_lft forever
2: enp0s25: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether f8:0f:41:65:37:d3 brd ff:ff:ff:ff:ff:ff
    inet 192.168.50.181/24 brd 192.168.50.255 scope global enp0s25
       valid_lft forever preferred_lft forever
    inet6 fd3d:c9f9:b50c:5da9:fa0f:41ff:fe65:37d3/64 scope global dynamic mngtmpaddr noprefixroute
       valid_lft 1660sec preferred_lft 1660sec
    inet6 fe80::fa0f:41ff:fe65:37d3/64 scope link
       valid_lft forever preferred_lft forever
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    link/ether 66:71:9e:cb:67:62 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
```

Entry 2: enp0s25

```sudo nano /etc/netplan/50-cloud-init.yaml```

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s25:
      dhcp4: no
      addresses:
        - 192.168.50.181/24
      routes:
        - to: default
          via: 192.168.50.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
      optional: no
```

---

## Install Plexmediaserver

- Download ```.deb``` package
- Run ```sudo dpkg -i "deb-package"```
- Go to ```http://127.0.0.1:32400/web```

### Uppgradera Plexmediaserver

- ```sudo apt-get update```
- ```sudo apt-get upgrade plexmediaserver```
- ```sudo systemctl restart plexmediaserver```

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

## Install DHCP Server

```sudo apt-get install isc-dhcp-server```

Config

```sudo nano -w /etc/dhcp/dhcpd.conf```

```bash
# minimal sample /etc/dhcp/dhcpd.conf
default-lease-time 600;
max-lease-time 7200;
    
subnet 192.168.1.0 netmask 255.255.255.0 {
 range 192.168.1.150 192.168.1.200;
 option routers 192.168.1.254;
 option domain-name-servers 192.168.1.1, 192.168.1.2;
 option domain-name "mydomain.example";
}
```

Start and stop service

```
sudo service isc-dhcp-server restart
sudo service isc-dhcp-server start
sudo service isc-dhcp-server stop
```


/etc/bind/named.conf.options: Global DNS options

/etc/bind/named.conf.local: For your zones

/etc/bind/named.conf.default-zones: Default zones such as localhost, its reverse, and the root hints

## Montera nätverksenhet permanent via SMB

* Installera *cifs-utils*

```bash
sudo apt install cifs-utils
```

* Förutsättning är en delad mapp med namnet *documents* från källenheten. Mappen delas med SMB-protokollet.
* Dela gärna med SMB3 i första hand alternativt SMB2 protokollet
* Skapa en mapp på målenheten (därifrån monteringen görs) med förslagsvis samma namn

```bash
sudo mkdir -p /mnt/documents
```

* Testmontera

```bash
sudo mount -t cifs //192.168.1.50/documents /mnt/documents \
  -o username=youruser,password=yourpassword,vers=3.0
```

Testa

```bash
ls /mnt/documents
```

Istället för att lägga lösenord i klartext rekommenderas en credentials-fil

```bash
sudo nano /etc/samba/creds-documents
```

Fyll i följande

```
username=youruser
password=yourpassword
```

Automatisera via /etc/fstab (montera vid boot)

```bash
sudo nano /etc/fstab
```

Fyll i

```bash
# Synology NAS share
//192.168.1.50/backup /mnt/nas cifs credentials=/etc/samba/creds-documents,iocharset=utf8,vers=3.0,nofail 0 0
```
