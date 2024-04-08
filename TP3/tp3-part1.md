# TP3 : Services

## I. Service SSH

## 1. Analyse du service

### 🌞 S'assurer que le service sshd est démarré

```
[kayss@node1 ~]$ systemctl status
● node1.tp2.b1
    State: running
    Units: 283 loaded (incl. loaded aliases)
     Jobs: 0 queued
   Failed: 0 units
    Since: Mon 2024-01-29 10:58:57 CET; 6min ago
  systemd: 252-13.el9_2
   CGroup: /
           ├─init.scope
           │ └─1 /usr/li ...
```

### 🌞 Analyser les processus liés au service SSH

```
[kayss@node1 ~]$ ps -ef | grep sshd
root         699       1  0 10:58 ?        00:00:00 sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups
root        1334     699  0 10:59 ?        00:00:00 sshd: kayss [priv]
kayss        1337    1334  0 10:59 ?        00:00:00 sshd: kayss@pts/0
kayss        1408    1338  0 11:10 pts/0    00:00:00 grep --color=auto sshd
```

### 🌞 Déterminer le port sur lequel écoute le service SSH

```
[kayss@node1 ~]$ sudo ss -alnpt | grep ssh
[sudo] password for kayss:
LISTEN 0      128          0.0.0.0:22        0.0.0.0:*    users:(("sshd",pid=699,fd=3))
LISTEN 0      128             [::]:22           [::]:*    users:(("sshd",pid=699,fd=4))
```

### 🌞 Consulter les logs du service SSH

```
[kayss@node1 log]$ sudo tail secure
Jan 29 11:03:56 node1 sudo[1389]: pam_unix(sudo:session): session closed for user root
Jan 29 11:14:12 node1 sudo[1425]:    kayss : TTY=pts/0 ; PWD=/home/kayss ; USER=root ; COMMAND=/sbin/ss -alnpt
Jan 29 11:14:12 node1 sudo[1425]: pam_unix(sudo:session): session opened for user root(uid=0) by kayss(uid=1000)
Jan 29 11:14:12 node1 sudo[1425]: pam_unix(sudo:session): session closed for user root
Jan 29 11:27:54 node1 sudo[1444]:    kayss : TTY=pts/0 ; PWD=/var/log ; USER=root ; COMMAND=/bin/tail secure
Jan 29 11:27:54 node1 sudo[1444]: pam_unix(sudo:session): session opened for user root(uid=0) by kayss(uid=1000)
Jan 29 11:27:54 node1 sudo[1444]: pam_unix(sudo:session): session closed for user root
Jan 29 11:28:11 node1 sudo[1449]:    kayss : TTY=pts/0 ; PWD=/var/log ; USER=root ; COMMAND=/bin/tail secure -n 37
Jan 29 11:28:11 node1 sudo[1449]: pam_unix(sudo:session): session opened for user root(uid=0) by kayss(uid=1000)
Jan 29 11:28:11 node1 sudo[1449]: pam_unix(sudo:session): session closed for user root
```

## 2. Modification du service

### 🌞 Identifier le fichier de configuration du serveur SSH

```
[kayss@node1 ssh]$ sudo find  -name "sshd_config"
./sshd_config
```

### 🌞 Modifier le fichier de conf

- exécutez un echo $RANDOM pour demander à votre shell de vous fournir un nombre aléatoire
```
[kayss@node1 ssh]$ echo $RANDOM
2176
```

- changez le port d'écoute du serveur SSH 

```
[kayss@node1 ssh]$ sudo cat sshd_config | grep "Port"
#Port 2176
```
- gérer le firewall

```
[kayss@node1 ssh]$ sudo firewall-cmd --remove-service ssh
[kayss@node1 ssh]$ sudo firewall-cmd --add-port=2176/tcp --permanent
success
[kayss@node1 ssh]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: cockpit dhcpv6-client
  ports: 80/tcp 2176/tcp
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

### 🌞 Redémarrer le service

```
[kayss@node1 ~]$ sudo systemctl restart sshd
[sudo] password for kayss:
[kayss@node1 ~]$
```

### 🌞 Effectuer une connexion SSH sur le nouveau port

```
PS C:\Users\ghass> ssh -p 2176 kayss@10.2.1.11
Last login: Mon Jan 29 11:56:52 2024 from 10.2.1.1
[kayss@node1 ~]$
```

