## II. Service HTTP

## 1. Mise en place

### 🌞 Installer le serveur NGINX

```
[kayss@node1 ~]$ sudo dnf install nginx
```

### 🌞 Démarrer le service NGINX
```
[kayss@node1 nginx]$ systemctl restart nginx.service
```

### 🌞 Déterminer sur quel port tourne NGINX

- vous devez filtrer la sortie de la commande utilisée pour n'afficher que les lignes demandées
```
[kayss@node1 ~]$ sudo ss -tlupn | grep nginx
tcp   LISTEN 0      511          0.0.0.0:80        0.0.0.0:*    users:(("nginx",pid=11576,fd=6),("nginx",pid=11575,fd=6))
tcp   LISTEN 0      511             [::]:80           [::]:*    users:(("nginx",pid=11576,fd=7),("nginx",pid=11575,fd=7))
```
- ouvrez le port concerné dans le firewall
```
[kayss@node1 ~]$ sudo firewall-cmd --list-all
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

### 🌞 Déterminer les processus liés au service NGINX

```
[kayss@node1 ~]$ ps -ef | grep nginx
root       11575       1  0 12:11 ?        00:00:00 nginx: master process nginx
nginx      11576   11575  0 12:11 ?        00:00:00 nginx: worker process
kayss       11633    1676  0 12:29 pts/0    00:00:00 grep --color=auto nginx
```

### 🌞 Déterminer le nom de l'utilisateur qui lance NGINX

```
[kayss@node1 ~]$ cat /etc/passwd | grep root
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin
```

### 🌞 Test !

```
[kayss@node1 ~]$ curl http://10.2.1.11:80 -s | head -n 7
<!doctype html>
<html>
  <head>
    <meta charset='utf-8'>
    <meta name='viewport' content='width=device-width, initial-scale=1'>
    <title>HTTP Server Test Page powered by: Rocky Linux</title>
    <style type="text/css">
```

## 2. Analyser la conf de NGINX

### 🌞 Déterminer le path du fichier de configuration de NGINX

```
[kayss@node1 nginx]$ ls -al /etc/nginx
total 84
drwxr-xr-x.  4 root root 4096 Jan 29 12:09 .
drwxr-xr-x. 78 root root 8192 Jan 30 10:29 ..
drwxr-xr-x.  2 root root    6 Oct 16 20:00 conf.d
drwxr-xr-x.  2 root root    6 Oct 16 20:00 default.d
-rw-r--r--.  1 root root 1077 Oct 16 20:00 fastcgi.conf
-rw-r--r--.  1 root root 1077 Oct 16 20:00 fastcgi.conf.default
-rw-r--r--.  1 root root 1007 Oct 16 20:00 fastcgi_params
-rw-r--r--.  1 root root 1007 Oct 16 20:00 fastcgi_params.default
-rw-r--r--.  1 root root 2837 Oct 16 20:00 koi-utf
-rw-r--r--.  1 root root 2223 Oct 16 20:00 koi-win
-rw-r--r--.  1 root root 5231 Oct 16 20:00 mime.types
-rw-r--r--.  1 root root 5231 Oct 16 20:00 mime.types.default
-rw-r--r--.  1 root root 2334 Oct 16 20:00 nginx.conf
-rw-r--r--.  1 root root 2656 Oct 16 20:00 nginx.conf.default
-rw-r--r--.  1 root root  636 Oct 16 20:00 scgi_params
-rw-r--r--.  1 root root  636 Oct 16 20:00 scgi_params.default
-rw-r--r--.  1 root root  664 Oct 16 20:00 uwsgi_params
-rw-r--r--.  1 root root  664 Oct 16 20:00 uwsgi_params.default
-rw-r--r--.  1 root root 3610 Oct 16 20:00 win-utf
```

### 🌞 Trouver dans le fichier de conf

```
[kayss@node1 nginx]$ cat nginx.conf | grep "server {" -A 16
    server {
        listen       80;
        listen       [::]:80;
        server_name  _;
        root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        error_page 404 /404.html;
        location = /404.html {
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
        }
    }
```

## 3. Déployer un nouveau site web

### 🌞 Créer un site web

```
[kayss@node1 www]$ sudo mkdir tp3_linux
[kayss@node1 www]$ ls
tp3_linux
[kayss@node1 www]$ cd tp3_linux/
[kayss@node1 tp3_linux]$ ls
[kayss@node1 tp3_linux]$ nano index.html


[kayss@node1 tp3_linux]$ cat index.html
<h1>Je m'appel pas</h1>
```

### 🌞 Gérer les permissions

```
[kayss@node1 tp3_linux]$ ls -l
total 4
-rw-r--r--. 1 root root 24 Jan 30 10:44 index.html
[kayss@node1 tp3_linux]$ man chmod
[kayss@node1 tp3_linux]$ sudo chmod 744 index.html
```

### 🌞 Adapter la conf NGINX

```
[kayss@node1 nginx]$ systemctl restart nginx.service
```

### 🌞 Visitez votre super site web

```
[kayss@node1 nginx]$ curl http://10.2.1.11:80
<h1>Je m'appel pas</h1>
```