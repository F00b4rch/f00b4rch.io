---
title: "Migrate Openvz To Lxc"
date: 2017-07-31T18:15:50Z
tags: [openVZ, LXC, proxmox, linux]
draft: false
---

J'ai récemment dû effectuer une migration de conteneurs d'un proxmox3 (sous OpenVZ) vers un proxmox4 (sous LXC).

Problème, il y a beaucoup de conteneurs à migrer/«convertir» pour tourner sous LXC. Il me fallait donc un moyen **d'automatiser** la procédure au maximum.

Par chance, la [documentation de migration](https://pve.proxmox.com/wiki/Convert_OpenVZ_to_LXC) est très bien détaillée. Je me suis donc appuyé sur cette dernière pour `basher` l'opération.

Vous retrouverez l'ensemble des sources sur mon [github](https://github.com/F00b4rch/vz2lxc)

J'ai coupé l'opération sous deux scripts, un script d'export et un autre d'import.

#### L'export :

Le script d'export prend deux paramètres en entrée : `l'ID du conteneur` et `l'IP` du serveur de destination.

Il faut également définir quelques variables qui serviront à l'envoi des données via `scp`.

La procédure *d'export* s'effectue de la manière suivante :

```
# Stop & Dump
sudo vzctl stop $ID && \
    echo "$ID stopped [OK]" && \
    sudo vzdump $ID -dumpdir /home/$USER/vzdump && \
    echo "$ID : dump [OK]" && \
```

On vérifie que le dump est bien présent :

```
# DumpName
vzDumpName=$(ls /home/$USER/vzdump/)

if [ -z $vzDumpName ] ; then
    echo "No dump found in /home/$USER/vzdump/"
    exit
fi
```

Puis on envoit vers le `Proxmox 4` :

```
sudo scp -i /home/$USER/.ssh/id_rsa "-P $rPort" $vzDumpName $rUSER@$rIP:$rPath && \
    echo "Sending Dump [OK]" && \
    sudo scp -i /home/$USER/.ssh/id_rsa "-P $rPort" vz.log $rUSER@$rIP:$rPath && \
    echo "Sending Log [OK]" && \
    sudo rm $vzDumpName && \
    sudo rm vz.log && \
    echo "SCP $vzDumpName on $rIP [OK]"
```

#### L'import :

Pour ce qui est de l'import, on ne passe qu'un argument en entrée qui est `l'ID` du conteneur.

Puis on démarre la partie **conversion** et **restauration**.

```
sudo pct restore $ID $dumpPath/$dumpName && \
    echo "pct $ID restoring [OK]"

# At this poin, you can set network configuration
# exemple : pct set 101 -net0 name=eth0,bridge=vmbr0,ip=192.168.15.144/24,gw=192.168.15.1
# I prefer doing it manually

sudo pct start $ID
sudo pct enter $ID
```

J'ai omis de vous préciser qu'il y a un petit système de file-log pour les opérations histoire de pouvoir tracer un peu le processus.
