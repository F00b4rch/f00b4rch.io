---
title: "Volume Additionnel Public Cloud Ovh"
date: 2017-07-31T11:38:18Z
tags: [ovh, linux, disque]
draft: false
---

Pour ajouter un disque/volume additionnel sur votre un public cloud OVH il faut effectuer une série de  manipulations.

Tout d'abord il faut identifier le nouveau disque :

    fdisk- l

Les retours peuvent varier selon le système (`sd{x}`, `vd{x}`).

Il faut ensuite créer une partition :
```
# parted /dev/{{disk}}
mktable gpt
mkpart primary ext4 512 100%
quit
```

La formater :

    mkfs -t ext4 -L rootfs /dev/{{disk}}1

Et enfin la monter :

    mount /dev/{{disk}}1 /mnt

Pour rendre persistant le montage, il faut passer par l'**UUID**.
On va donc récupérer l'ID du bloc :

```
# blkid
/dev/sdb1: LABEL="rootfs" UUID="6b75bbb4-b311-4b9d-a8fd-6e6ff23c401f" TYPE="ext4" PARTLABEL="primary" 
PARTUUID="e20dc227-9d10-41c4-a714-2fb53d190c11"
/dev/sda1: UUID="9abb590f-8a5e-496f-ad2a-2c877415bdc5" TYPE="ext4" PARTUUID="71036eb1-01"
```

Et ajouter dans le fichier `/etc/fstab` les informations de montage :

```
# vim /etc/fstab
[...]
UUID=6b75bbb4-b311-4b9d-a8fd-6e6ff23c401f /mnt ext4 errors=remount-ro,discard 0 1
```

Vous pouvez tester la bonne configuration avec un reboot.

