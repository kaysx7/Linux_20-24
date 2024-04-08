# Partie 3 : Serveur web

## 2. Install

### 🌞 Installez NGINX

```
[kayss@web ~]$ sudo dnf install nginx
```

## 3. Analyse

### 🌞 Analysez le service NGINX

- avec une commande ps, déterminer sous quel utilisateur tourne le processus du service NGINX :

```
[kayss@web ~]$ ps aux | grep nginx
root       12010  0.0  0.1  10108   956 ?        Ss   11:12   0:00 nginx: master process /usr/sbin/nginx
nginx      12011  0.0  0.6  13908  4812 ?        S    11:12   0:00 nginx: worker process
kayss       12026  0.0  0.2   6408  2180 pts/0    S+   11:14   0:00 grep --color=auto nginx
```

- avec une commande ss, déterminer derrière quel port écoute actuellement le serveur web :

```
[kayss@web ~]$ ss -alpnt
State          Recv-Q         Send-Q                  Local Address:Port                   Peer Address:Port         Process
LISTEN         0              511                           0.0.0.0:80                          0.0.0.0:*
LISTEN         0              4096                          0.0.0.0:111                         0.0.0.0:*
LISTEN         0              128                           0.0.0.0:22                          0.0.0.0:*
LISTEN         0              511                              [::]:80                             [::]:*
LISTEN         0              4096                             [::]:111                            [::]:*
LISTEN         0              128                              [::]:22                             [::]:*
```

- en regardant la conf, déterminer dans quel dossier se trouve la racine web :

```
[kayss@web ~]$ grep -R "root" /etc/nginx/nginx.conf
        root         /usr/share/nginx/html;
#        root         /usr/share/nginx/html;
```

- inspectez les fichiers de la racine web, et vérifier qu'ils sont bien accessibles en lecture par l'utilisateur qui lance le processus

```
[kayss@web ~]$  ls -l /usr/share/nginx/html
total 12
-rw-r--r--. 1 root root 3332 Oct 16 19:58 404.html
-rw-r--r--. 1 root root 3404 Oct 16 19:58 50x.html
drwxr-xr-x. 2 root root   27 Feb 20 11:10 icons
lrwxrwxrwx. 1 root root   25 Oct 16 20:00 index.html -> ../../testpage/index.html
-rw-r--r--. 1 root root  368 Oct 16 19:58 nginx-logo.png
lrwxrwxrwx. 1 root root   14 Oct 16 20:00 poweredby.png -> nginx-logo.png
lrwxrwxrwx. 1 root root   37 Oct 16 20:00 system_noindex_logo.png -> ../../pixmaps/system-noindex-logo.png
```
## 4. Visite du service web

### 🌞 Configurez le firewall pour autoriser le trafic vers le service NGINX

```
[kayss@web ~]$ sudo firewall-cmd --zone=public --add-port=80/tcp --permanent
[sudo] password for kayss:
Warning: ALREADY_ENABLED: 80:tcp
success
[kayss@web ~]$ sudo firewall-cmd --reload
success
```

### 🌞 Accéder au site web

```
PS C:\Users\ghass> curl http://10.2.1.12:80


StatusCode        : 200
StatusDescription : OK
Content           : <!doctype html>
                    <html>
                      <head>
                        <meta charset='utf-
```

### 🌞 Vérifier les logs d'accès

```
[kayss@web ~]$ tail -n 3 /var/log/nginx/access.log
10.2.1.1 - - [20/Feb/2024:11:30:58 +0100] "GET /poweredby.png HTTP/1.1" 200 368 "http://10.2.1.12/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.0.0 Safari/537.36" "-"
10.2.1.1 - - [20/Feb/2024:11:30:58 +0100] "GET /favicon.ico HTTP/1.1" 404 3332 "http://10.2.1.12/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.0.0 Safari/537.36" "-"
10.2.1.1 - - [20/Feb/2024:11:31:46 +0100] "GET / HTTP/1.1" 200 7620 "-" "Mozilla/5.0 (Windows NT; Windows NT 10.0; fr-FR) WindowsPowerShell/5.1.22621.2506" "-"
```

## 5. Modif de la conf du serveur web

### 🌞 Changer le port d'écoute

```
[kayss@web ~]$ sudo nano /etc/nginx/nginx.conf
(on modifie le 80 par le 8080)
```
- prouvez-moi que le changement a pris effet avec une commande ss
```
[kayss@web ~]$ ss -tuln | grep 8080
tcp   LISTEN 0      511          0.0.0.0:8080      0.0.0.0:*
tcp   LISTEN 0      511             [::]:8080         [::]:*
```
- n'oubliez pas de fermer l'ancien port dans le firewall, et d'ouvrir le nouveau

```
[kayss@web ~]$ sudo firewall-cmd --remove-port=80/tcp --permanent
success
```

- prouvez avec une commande curl sur votre machine que vous pouvez désormais visiter le port 8080

```
PS C:\Users\ghass> curl http://10.2.1.12:8080


StatusCode        : 200
StatusDescription : OK
Content           : <!doctype html>
                    <html>
```

### 🌞 Changer l'utilisateur qui lance le service

```
[kayss@web ~]$  ps aux | grep nginx
root       12198  0.0  0.1  10108   956 ?        Ss   11:58   0:00 nginx: master process /usr/sbin/nginx
web        12199  0.0  0.6  13908  4792 ?        S    11:58   0:00 nginx: worker process
kayss       12201  0.0  0.2   6408  2176 pts/0    S+   11:58   0:00 grep --color=auto nginx
```

### 🌞 Changer l'emplacement de la racine Web
