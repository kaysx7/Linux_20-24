## III. Your own services

## 1. Au cas où vous l'auriez oublié

## 2. Analyse des services existants

### 🌞 Afficher le fichier de service SSH

```
[kayss@node1 nginx]$ cat /usr/lib/systemd/system/sshd.service | grep ExecStart
ExecStart=/usr/sbin/sshd -D $OPTIONS
```

### 🌞 Afficher le fichier de service NGINX

```
[kayss@node1 nginx]$ cat /usr/lib/systemd/system/nginx.service | grep ExecStart=
ExecStart=/usr/sbin/nginx
```

## 3. Création de service

### 🌞 Créez le fichier /etc/systemd/system/tp3_nc.service

```
[kayss@node1 nginx]$ echo $RANDOM
4617

[kayss@node1 nginx]$ sudo nano /etc/systemd/system/tp3_nc.service
```

### 🌞 Indiquer au système qu'on a modifié les fichiers de service

```
[kayss@node1 nginx]$ sudo systemctl daemon-reload
```

### 🌞 Démarrer notre service de ouf

```
[kayss@node1 nginx]$ sudo systemctl start tp3_nc.service
```

### 🌞 Vérifier que ça fonctionne

```
[kayss@node1 nginx]$ systemctl status tp3_nc.service

[kayss@node1 nginx]$  sudo ss -alnpt | grep nc
LISTEN 0      10           0.0.0.0:4617      0.0.0.0:*    users:(("nc",pid=1918,fd=4))
LISTEN 0      10              [::]:4617         [::]:*    users:(("nc",pid=1918,fd=3))

[kayss@node1 nginx]$ sudo systemctl status tp3_nc
● tp3_nc.service - Super netcat tout fou
     Loaded: loaded (/etc/systemd/system/tp3_nc.service; static)
     Active: active (running) since Tue 2024-01-30 11:32:58 CET>
   Main PID: 1918 (nc)
      Tasks: 1 (limit: 4673)
     Memory: 788.0K
        CPU: 4ms
     CGroup: /system.slice/tp3_nc.service
             └─1918 /usr/bin/nc -l 4617 -k

Jan 30 11:32:58 node1.tp2.b1 systemd[1]: Started Super netcat t>
Jan 30 11:45:41 node1.tp2.b1 nc[1918]: llele
Jan 30 11:45:41 node1.tp2.b1 nc[1918]: le
Jan 30 11:45:41 node1.tp2.b1 nc[1918]: le
Jan 30 11:45:42 node1.tp2.b1 nc[1918]: el
Jan 30 11:45:42 node1.tp2.b1 nc[1918]: el
Jan 30 11:45:42 node1.tp2.b1 nc[1918]: el
Jan 30 11:45:42 node1.tp2.b1 nc[1918]: ele
```

### 🌞 Les logs de votre service

- une commande journalctl filtrée avec grep qui affiche la ligne qui indique le démarrage du service
```
[kayss@node1 nginx]$ journalctl | grep "systemctl start"
Jan 30 11:32:58 node1.tp2.b1 sudo[1914]:     kayss : TTY=pts/0 ; PWD=/etc/nginx ; USER=root ; COMMAND=/bin/systemctl start tp3_nc.service
```

- une commande journalctl filtrée avec grep qui affiche un message reçu qui a été envoyé par le client

```
[kayss@node1 nginx]$ journalctl | grep coucou
Jan 30 11:46:45 node1.tp2.b1 nc[1918]: coucou
```

- une commande journalctl filtrée avec grep qui affiche la ligne qui indique l'arrêt du service

```
[kayss@node1 nginx]$ journalctl | grep finished | tail -n 1
Jan 30 12:01:01 node1.tp2.b1 run-parts[2045]: (/etc/cron.hourly) finished 0anacron
```

### 🌞 S'amuser à kill le processus

- repérez le PID du processus nc lancé par votre service
```
[kayss@node1 nginx]$ ps -ef | grep nc | grep root
root        1918       1  0 11:32 ?        00:00:00 /usr/bin/nc -l 4617 -k
```
- utilisez la commande kill pour mettre fin à ce processus nc
```
[kayss@node1 nginx]$ sudo kill 1918
[sudo] password for kayss:
[kayss@node1 nginx]$
```

### 🌞 Affiner la définition du service

- ajoutez Restart=always dans la section [Service] de votre service
```
[kayss@node1 nginx]$ cat /usr/lib/systemd/system/sshd.service | grep Restart
Restart=always
```
- n'oubliez pas d'indiquer au système que vous avez modifié les fichiers de service :)
```
[kayss@node1 nginx]$ sudo systemctl restart nginx.service
```

- normalement, quand tu kill il est donc relancé automatiquement

```
[kayss@node1 ~]$  ps -fe | grep nc
dbus         657       1  0 08:58 ?        00:00:00 /usr/bin/dbus-broker-launch --scope system --audit
root         693       1  0 08:58 ?        00:00:00 login -- kayss
kayss      839       1  0 09:04 ?        00:00:00 /usr/lib/systemd/systemd --user
kayss      841     839  0 09:04 ?        00:00:00 (sd-pam)
kayss      849     693  0 09:04 tty1     00:00:00 -bash
root       43166   18011  0 12:01 ?        00:00:00 sshd: kayss [priv]
kayss    43170   43166  0 12:01 ?        00:00:00 sshd: kayss@pts/0
kayss    43171   43170  0 12:01 pts/0    00:00:00 -bash
root       43265       1  0 12:29 ?        00:00:00 /usr/bin/nc -l 13584 -k
kayss    43266   43171  0 12:29 pts/0    00:00:00 ps -fe
kayss    43267   43171  0 12:29 pts/0    00:00:00 grep --color=auto nc
```