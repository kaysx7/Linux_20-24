# Partie 2 : Serveur de partage de fichiers

### 🌞 Donnez les commandes réalisées sur le serveur NFS storage.tp4.linux

```
[kayss@storage storage]$ sudo yum install nfs-utils
[kayss@storage storage]$ sudo mkdir site_web_1
[kayss@storage storage]$ sudo mkdir site_web_2
[kayss@storage storage]$ sudo nano /etc/exports
(/storage/site_web_1 10.2.1.12(rw,sync,no_root_squash)
/storage/site_web_2 10.2.1.12.1 rw,sync,no_root_squash)
```

### 🌞 Donnez les commandes réalisées sur le client NFS web.tp4.linux

```
[kayss@web ~]$ sudo yum install nfs-utils
[kayss@web ~]$  sudo mkdir -p /var/www/site_web_1
[kayss@web ~]$ sudo mkdir -p /var/www/site_web_2
[kayss@web ~]$  sudo nano /etc/fstab
(10.2.1.11:/storage/site_web_1 /var/www/site_web_1 nfs defaults 0 0
10.2.1.11:/storage/site_web_2 /var/www/site_web_2 nfs defaults 0 0
)
[kayss@web ~]$ systemctl daemon-reload
Failed to reload daemon: Access denied
[kayss@web ~]$ sudo !!
sudo systemctl daemon-reload
[kayss@web ~]$ ls -ld /var/www/site_web_1 /var/www/site_web_2
drwxr-xr-x. 2 root root 6 Feb 20 10:53 /var/www/site_web_1
drwxr-xr-x. 2 root root 6 Feb 20 10:53 /var/www/site_web_2
```