# TP4 : Real services
## Partie 1 : Partitionnement du serveur de stockage

### 🌞 Partitionner le disque à l'aide de LVM

```
[kayss@storage ~]$ lsblk
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda           8:0    0   20G  0 disk
├─sda1        8:1    0    1G  0 part /boot
└─sda2        8:2    0   19G  0 part
  ├─rl-root 253:0    0   17G  0 lvm  /
  └─rl-swap 253:1    0    2G  0 lvm  [SWAP]
sdb           8:16   0    2G  0 disk
sdc           8:32   0    2G  0 disk
sr0          11:0    1 1024M  0 rom
```

- créer un physical volume (PV) :

```
[kayss@storage ~]$ sudo pvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VB6cd12894-269c762c PVID kRMp4Uapn4k5UUqPeHFgdDoszHL86p8Q last seen on /dev/sda2 not found.
  PV         VG Fmt  Attr PSize PFree
  /dev/sdb      lvm2 ---  2.00g 2.00g
  /dev/sdc      lvm2 ---  2.00g 2.00g
```
- créer un nouveau volume group (VG)
```
[kayss@storage ~]$ sudo vgs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VB6cd12894-269c762c PVID kRMp4Uapn4k5UUqPeHFgdDoszHL86p8Q last seen on /dev/sda2 not found.
  VG      #PV #LV #SN Attr   VSize VFree
  storage   2   0   0 wz--n- 3.99g 3.99g
```

- créer un nouveau logical volume (LV) : ce sera la partition utilisable

```
[kayss@storage ~]$ sudo lvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VB6cd12894-269c762c PVID kRMp4Uapn4k5UUqPeHFgdDoszHL86p8Q last seen on /dev/sda2 not found.
  LV      VG      Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  storage storage -wi-a----- 3.99g
```

### 🌞 Formater la partition

```
[kayss@storage ~]$ sudo mkfs -t ext4 /dev/storage/storage
mke2fs 1.46.5 (30-Dec-2021)
Creating filesystem with 1046528 4k blocks and 261632 inodes
Filesystem UUID: 033b80cc-c6b4-41da-8949-63d54b61342a
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736

Allocating group tables: done
Writing inode tables: done
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done
```

### 🌞 Monter la partition

```
[kayss@storage storage]$ df -h | grep storage
/dev/mapper/storage-storage  3.9G  401M  3.3G  11% /storage
```
- prouvez que vous pouvez lire et écrire des données sur cette partition
```
[kayss@storage /]$ ls -al | grep storage
drwxr-xr-x.   3 root root 4096 Feb 19 12:14 storage
```
- définir un montage automatique de la partition (fichier /etc/fstab)
```
/dev/storage/storage /storage ext4 defaults 0 0
```

