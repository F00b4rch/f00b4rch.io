---
title: "Freebsd Pci Disk Space"
date: 2018-03-04T14:56:33Z
draft: false
tags: [freebsd, ovh, serveur, publicloud]
---

Quand vous créez une instance OVH Public Cloud sous Freebsd avec un certain espace disque, disons 50G, vous vous apercevrez qu'il n'est pas appliqué sur votre partition.

Tout d'abord regardons ce que nous avons :
```
# gpart show
=>      40  10239920  da0  GPT  (50G) [CORRUPT]
        40      1024    1  freebsd-boot  (512K)
      1064       984       - free -  (492K)
      2048  10235904    2  freebsd-zfs  (4.9G)
  10237952      2008       - free -  (1.0M)
```

On note que notre volume da0 est tagué en _CORRUPT_. Pas de panique, tout le monde sait que le handbook de Freebsd est génial. Je cite :

> If the disk was formatted with the GPT partitioning scheme, it may show as “corrupted” because the GPT backup partition table is no longer at the end of the drive. Fix the backup partition table with gpart:
```
# gpart recover ada0
ada0 recovered
```
>

Bon, on va donc applique ça à notre serveur en remplaçant `ada0` par `da0` :
```
# gpart recover da0
da0 recovered
```

Vérifions :
```
# gpart show
=>       40  104857520  da0  GPT  (50G)
         40       1024    1  freebsd-boot  (512K)
       1064        984       - free -  (492K)
       2048   10235904    2  freebsd-zfs  (4.9G)
   10237952   94619608       - free -  (45G)
```

C'est tout de suite beaucoup mieux !

Nous voyons donc que notre "disk" da0 dispose de 50G. Toutefois si on regarde de plus près notre système, on s'aperçoit que tout l'espace n'est pas présent.
```
# df -h
Filesystem            Size    Used   Avail Capacity  Mounted on
zroot/ROOT/default    4.7G    493M    4.2G    10%    /
devfs                 1.0K    1.0K      0B   100%    /dev
zroot                 4.2G     96K    4.2G     0%    /zroot
```

Une nouvelle fois, pas de panique. Le handbook est notre ami. C'est notre rais à nous !

Appliquons donc les 45G free sur notre partition :
```
# gpart resize -i 2 -a 4k -s 50G da0
da0p2 resized
```

Vérifions :
```
# gpart show
=>       40  104857520  da0  GPT  (50G)
         40       1024    1  freebsd-boot  (512K)
       1064        984       - free -  (492K)
       2048   94371840    2  freebsd-zfs  (45G)
   94373888   10483672       - free -  (5.0G)
```

Bien, nous avançons, toutefois, l'espace n'est pas encore utilisable comme nous l'indique le retour de df :
```
# df -h
Filesystem            Size    Used   Avail Capacity  Mounted on
zroot/ROOT/default    4.7G    493M    4.2G    10%    /
devfs                 1.0K    1.0K      0B   100%    /dev
zroot                 4.2G     96K    4.2G     0%    /zroot
```

Il faut demander à notre zpool d'utiliser cet espace.

Commençons d'abord par regarder l'état de notre pool.
```
# zpool status
  pool: zroot
 state: ONLINE
  scan: none requested
config:

	NAME        STATE     READ WRITE CKSUM
	zroot       ONLINE       0     0     0
	  da0p2     ONLINE       0     0     0
```

Indiquons que nous voulons l'autoexpand sur zroot :
```
# zpool set autoexpand=on zroot
```

Et appliquons l'ajout sur da0p2 :
```
# zpool online -e zroot /dev/da0p2
```

Voilà, vérifions :
```
# df -h
Filesystem            Size    Used   Avail Capacity  Mounted on
zroot/ROOT/default     44G    493M     43G     1%    /
devfs                 1.0K    1.0K      0B   100%    /dev
zroot                  43G     96K     43G     0%    /zroot
```

Le tour est joué !
