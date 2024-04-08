# TP1 : Casser avant de construire

## II. Casser

### 2. Fichier

🌞 Supprimer des fichiers
``````
[kayss@localhost var]$ sudo rm -r tmp
[kayss@localhost var]$ sudo reboot
[sudo] password for kayss:
[kayss@localhost var]$ Connection to 10.3.1.11 closed by remote host.
Connection to 10.3.1.11 closed.
``````
### 3. Utilisateurs

🌞 Mots de passe


- ####  changez le mot de passe de tous les utilisateurs qui en ont déjà un

``````
[root@localhost ~]# sudo -i
[root@localhost ~]# passwd
Changing password for user root.
New password:
passwd: all authentication tokens updated successfully.
``````
- #### trouvez donc un moyen de lister les utilisateurs, et trouver ceux qui ont déjà un mot de passe

``````
[root@localhost ~]# sudo cat /etc/shadow
root:$6$YMp8sJz13UxPyJ6p$fYHmJoqtDY/DbMFUv.F5burztbstnbNBYHytdrxEO594Cj.30kDmUJ4VkE3bKb9Ybsug35aMmYu3YhmHgqzul.:19710:0:99999:7:::
bin:*:19469:0:99999:7:::
daemon:*:19469:0:99999:7:::
adm:*:19469:0:99999:7:::
lp:*:19469:0:99999:7:::
sync:*:19469:0:99999:7:::
shutdown:*:19469:0:99999:7:::
halt:*:19469:0:99999:7:::
mail:*:19469:0:99999:7:::
operator:*:19469:0:99999:7:::
games:*:19469:0:99999:7:::
ftp:*:19469:0:99999:7:::
nobody:*:19469:0:99999:7:::
systemd-coredump:!!:19653::::::
dbus:!!:19653::::::
tss:!!:19653::::::
sssd:!!:19653::::::
sshd:!!:19653::::::
chrony:!!:19653::::::
systemd-oom:!*:19653::::::
kayss:$6$pD7g4ccE4w32hs7R$.GMkpxJD1X/UdHVeEoj8gxtU/gRUy4K27IfErb9TSUU45n3vnSu.0Qij75QJNFHHT7qW68KvNY6Kyw/q1kBiL1::0:99999:7:::
tcpdump:!!:19653::::::
``````

🌞 Another way ?
````
[kayss@localhost ~]$ sudo touch /etc/nologin
[sudo] password for kayss:
[kayss@localhost ~]$
logout
Connection to 10.5.1.4 closed.
PS C:\Users\kayss> ssh kayss@10.5.1.4
kayss@10.5.1.4's password:
Connection closed by 10.5.1.4 port 22`
``````
### 4. Disques

🌞 Effacer le contenu du disque dur
``````
[kayss@localhost dev]$ sudo dd if=/dev/zero of=/dev/sda bs=4M status=progress
[sudo] password for kayss:
3590324224 bytes (3.6 GB, 3.3 GiB) copied, 62 s, 58.2 MB/s^C
856+0 records in
856+0 records out
3590324224 bytes (3.6 GB, 3.3 GiB) copied, 61.6928 s, 58.2 MB/s
``````
### 5. Malware

🌞 Reboot automatique
````
if [ -z "$REBOOT_TRIGGERED" ]; then
    export REBOOT_TRIGGERED=true
    echo "vite t'as pas le temps"
    (sleep 6 && sudo reboot) 
``````
### 6. You own way

🌞 Trouvez 4 autres façons de détuire la machine
````
 [ -z "$REBOOT_TRIGGERED"]; then
    export REBOOT_TRIGGERED=true
    sudo cat /dev/mapper/rl-root &
    sudo cat /dev/mapper/rl-root
````
