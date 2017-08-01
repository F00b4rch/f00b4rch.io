---
title: "Régler l’heure et la date Vm Proxmox"
date: 2017-07-30T12:44:46Z
tags: [proxmox, lxc, openvz, linux]
draft: false
---
Que l’on tourne sur de l’OpenVZ ou du LXC, il peut arriver que sur certains template proposé par Proxmox, le paramétrage de la date et l’heure du système ne soit pas bon.  

Si la procédure de réglage habituelle ne fonctionne pas :  

**Pour OpenVZ :**

    # On entre dans le container
    vzctl enter ID
    # On effectue un backup
    mv /etc/localtime /etc/localtime.old
    # On ajoute un lien symbolique
    ln -s /usr/share/zoneinfo/Europe/Paris /etc/localtime
    # Il ne reste plus qu'à vérifier
    date

**Pour LXC :**

    # On entre dans le container
    pct enter ID
    # On effectue un backup
    mv /etc/localtime /etc/localtime.old
    # On ajoute un lien symbolique
    ln -s /usr/share/zoneinfo/Europe/Paris /etc/localtime
    # Il ne reste plus qu'à vérifier
    date
